---
layout: page
title: The `dpgen` Cheat Sheet
published: true
tags: deepmodling dpgen package cheatsheet
---

This posts lists the keys in `param.json` and `machine.json`, and mainly for
reference use.

**Initially copied from** the [dpgen official manual](https://github.com/deepmodeling/dpgen/blob/master/README.md), which is now (20210714) in markdown format.



### The descriptions of keys in `PARAM.json`

The following table gives explicit descriptions on keys in `PARAM`.

The bold notation of key (such aas **type_map**) means that it's a necessary key.



| Key                              | Type                                                         | Example                                                      | Discription                                                  |
| -------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| *#Basics*                        |                                                              |                                                              |                                                              |
| **type_map**                     | List of string                                               | ["H", "C"]                                                   | Atom types                                                   |
| **mass_map**                     | List of float                                                | [1, 12]                                                      | Standard atom weights.                                       |
| **use_ele_temp**                 | int                                                          | 0                                                            | Currently only support fp_style vasp. 0(default): no electron temperature. 1: eletron temperature as frame parameter. 2: electron temperature as atom parameter. |
| *#Data*                          |                                                              |                                                              |                                                              |
| init_data_prefix                 | String                                                       | "/sharedext4/.../data/"                                      | Prefix of initial data directories                           |
| ***init_data_sys***              | List of string                                               | ["CH4.POSCAR.01x01x01/.../deepmd"]                           | Directories of initial data. You may use either absolute or relative path here. |
| ***sys_format***                 | String                                                       | "vasp/poscar"                                                | Format of initial data. It will be `vasp/poscar` if not set. |
| init_multi_systems               | Boolean                                                      | false                                                        | If set to `true`, `init_data_sys` directories should contain sub-directories of various systems. DP-GEN will regard all of these sub-directories as inital data systems. |
| **init_batch_size**              | String of integer                                            | [8]                                                          | Each number is the batch_size of corresponding system for training in `init_data_sys`. One recommended rule for setting the `sys_batch_size` and `init_batch_size` is that `batch_size`mutiply number of atoms ot the stucture should be larger than 32. If set to `auto`, batch size will be 32 divided by number of atoms. |
| sys_configs_prefix               | String                                                       | "/sharedext4/.../data/"                                      | Prefix of `sys_configs`                                      |
| **sys_configs**                  | List of list of string                                       | [ ["/sharedext4/.../POSCAR"],  ["....../POSCAR"] ]           | Containing directories of structures to be explored in iterations.Wildcard characters are supported here. |
| **sys_batch_size**               | List of integer                                              | [8, 8]                                                       | Each number is the batch_size for training of corresponding system in `sys_configs`. If set to `auto`, batch size will be 32 divided by number of atoms. |
| *#Training*                      |                                                              |                                                              |                                                              |
| **numb_models**                  | Integer                                                      | 4 (recommend)                                                | Number of models to be trained in `00.train`.                |
| training_iter0_model_path        | list of string                                               | ["/path/to/model0_ckpt/", ...]                               | The model used to init the first iter training. Number of element should be equal to `numb_models` |
| training_init_model              | bool                                                         | False                                                        | Iteration > 0, the model parameters will be initilized from the model trained at the previous iteration. Iteration == 0, the model parameters will be initialized from `training_iter0_model_path`. |
| **default_training_param**       | Dict                                                         | { ...  "use_smooth": true,  "sel_a": [16, 4],  "rcut_smth": 0.5,  "rcut": 5,  "filter_neuron": [10, 20, 40],  ... } | Training parameters for `deepmd-kit` in `00.train`.  You can find instructions from here: (https://github.com/deepmodeling/deepmd-kit).. We commonly let `stop_batch` = 200 * `decay_steps`. |
| *#Exploration*                   |                                                              |                                                              |                                                              |
| **model_devi_dt**                | Float                                                        | 0.002 (recommend)                                            | Timestep for MD                                              |
| **model_devi_skip**              | Integer                                                      | 0                                                            | Number of structures skipped for fp in each MD               |
| **model_devi_f_trust_lo**        | Float                                                        | 0.05                                                         | Lower bound of forces for the selection.                     |
| **model_devi_f_trust_hi**        | Float                                                        | 0.15                                                         | Upper bound of forces for the selection                      |
| **model_devi_e_trust_lo**        | Float                                                        | 1e10                                                         | Lower bound of energies for the selection. Recommend to set them a high number, since forces provide more precise information. Special cases such as energy minimization may need this. |
| **model_devi_e_trust_hi**        | Float                                                        | 1e10                                                         | Upper bound of energies for the selection.                   |
| **model_devi_clean_traj**        | Boolean                                                      | true                                                         | Deciding whether to clean traj folders in MD since they are too large. |
| **model_devi_nopbc**             | Boolean                                                      | False                                                        | Assume open boundary condition in MD simulations.            |
| model_devi_activation_func       | List of String                                               | ["tanh", "tanh", "tanh", "tanh"]                             | Set activation functions for models, length of the list should be the same as `numb_models` |
| **model_devi_jobs**              | [ { "sys_idx": [0],  "temps":  [100], "press": [1], "trj_freq": 10, "nsteps": 1000, "ensembles":  "nvt"  }, ... ] | List of dict                                                 | Settings for exploration in `01.model_devi`. Each dict in the list corresponds to one iteration. The index of `model_devi_jobs`exactly accord with index of iterations |
| **model_devi_jobs["sys_idx"]**   | List of integer                                              | [0]                                                          | Systems to be selected as the initial structure of MD and be explored. The index corresponds exactly to the `sys_configs`. |
| **model_devi_jobs["temps"]**     | List of integer                                              | [50, 300]                                                    | Temperature (**K**) in MD                                    |
| **model_devi_jobs["press"]**     | List of integer                                              | [1,10]                                                       | Pressure (**Bar**) in MD                                     |
| **model_devi_jobs["trj_freq"]**  | Integer                                                      | 10                                                           | Frequecy of trajectory saved in MD.                          |
| **model_devi_jobs["nsteps"]**    | Integer                                                      | 3000                                                         | Running steps of MD.                                         |
| **model_devi_jobs["ensembles"]** | String                                                       | "nvt"                                                        | Determining which ensemble used in MD, **options** include “npt” and “nvt”. |
| model_devi_jobs["neidelay"]      | Integer                                                      | "10"                                                         | delay building until this many steps since last build        |
| model_devi_jobs["taut"]          | Float                                                        | "0.1"                                                        | Coupling time of thermostat (ps)                             |
| model_devi_jobs["taup"]          | Float                                                        | "0.5"                                                        | Coupling time of barostat (ps)                               |
| *#Labeling*                      |                                                              |                                                              |                                                              |
| **fp_style**                     | string                                                       | "vasp"                                                       | Software for First Principles. **Options**include “vasp”, “pwscf”, “siesta” and “gaussian” up to now. |
| **fp_task_max**                  | Integer                                                      | 20                                                           | Maximum of structures to be calculated in `02.fp` of each iteration. |
| **fp_task_min**                  | Integer                                                      | 5                                                            | Minimum of structures to calculate in `02.fp` of each iteration. |
| fp_accurate_threshold            | Float                                                        | 0.9999                                                       | If the accurate ratio is larger than this number, no fp calculation will be performed, i.e. fp_task_max = 0. |
| fp_accurate_soft_threshold       | Float                                                        | 0.9999                                                       | If the accurate ratio is between this number and `fp_accurate_threshold`, the fp_task_max linearly decays to zero. |
| fp_cluster_vacuum                | Float                                                        | None                                                         | If the vacuum size is smaller than this value, this cluster will not be choosen for labeling |
| *fp_style == VASP*               |                                                              |                                                              |                                                              |
| **fp_pp_path**                   | String                                                       | "/sharedext4/.../ch4/"                                       | Directory of psuedo-potential file to be used for 02.fp exists. |
| **fp_pp_files**                  | List of string                                               | ["POTCAR"]                                                   | Psuedo-potential file to be used for 02.fp. Note that the order of elements should correspond to the order in `type_map`. |
| **fp_incar**                     | String                                                       | "/sharedext4/../ch4/INCAR"                                   | Input file for VASP. INCAR must specify KSPACING and KGAMMA. |
| **fp_aniso_kspacing**            | List of integer                                              | [1.0,1.0,1.0]                                                | Set anisotropic kspacing. Usually useful for 1-D or 2-D materials. Only support VASP. If it is setting the KSPACING key in INCAR will be ignored. |
| cvasp                            | Boolean                                                      | true                                                         | If `cvasp` is true, DP-GEN will use Custodian to help control VASP calculation. |
| *fp_style == Gaussian*           |                                                              |                                                              |                                                              |
| **use_clusters**                 | Boolean                                                      | false                                                        | If set to `true`, clusters will be taken instead of the whole system. This option does not work with DeePMD-kit 0.x. |
| **cluster_cutoff**               | Float                                                        | 3.5                                                          | The cutoff radius of clusters if `use_clusters` is set to `true`. |
| **fp_params**                    | Dict                                                         |                                                              | Parameters for Gaussian calculation.                         |
| **fp_params["keywords"]**        | String or list                                               | "mn15/6-31g** nosymm scf(maxcyc=512)"                        | Keywords for Gaussian input.                                 |
| **fp_params["multiplicity"]**    | Integer or String                                            | 1                                                            | Spin multiplicity for Gaussian input. If set to `auto`, the spin multiplicity will be detected automatically. If set to `frag`, the "fragment=N" method will be used. |
| **fp_params["nproc"]**           | Integer                                                      | 4                                                            | The number of processors for Gaussian input.                 |
| *fp_style == siesta*             |                                                              |                                                              |                                                              |
| **use_clusters**                 | Boolean                                                      | false                                                        | If set to `true`, clusters will be taken instead of the whole system. This option does not work with DeePMD-kit 0.x. |
| **cluster_cutoff**               | Float                                                        | 3.5                                                          | The cutoff radius of clusters if `use_clusters` is set to `true`. |
| **fp_params**                    | Dict                                                         |                                                              | Parameters for siesta calculation.                           |
| **fp_params["ecut"]**            | Integer                                                      | 300                                                          | Define the plane wave cutoff for grid.                       |
| **fp_params["ediff"]**           | Float                                                        | 1e-4                                                         | Tolerance of Density Matrix.                                 |
| **fp_params["kspacing"]**        | Float                                                        | 0.4                                                          | Sample factor in Brillouin zones.                            |
| **fp_params["mixingweight"]**    | Float                                                        | 0.05                                                         | Proportion a of output Density Matrix to be used for the input Density Matrix of next SCF cycle (linear mixing). |
| **fp_params["NumberPulay"]**     | Integer                                                      | 5                                                            | Controls the Pulay convergence accelerator.                  |
| *fp_style == cp2k*               |                                                              |                                                              |                                                              |
| **user_fp_params**               | Dict                                                         |                                                              | Parameters for cp2k calculation. find detail in manual.cp2k.org. only the kind section must be set before use. we assume that you have basic knowledge for cp2k input. |
| **external_input_path**          | String                                                       |                                                              | Conflict with key:user_fp_params, use the template input provided by user, some rules should be followed, read the following text in detail. |



### The descriptions of keys in `MACHINE.json`

| Key                                | Type                 | Example                       | Discription                                                  |
| ---------------------------------- | -------------------- | ----------------------------- | ------------------------------------------------------------ |
| deepmd_path                        | String               | "......tf1120-lowprec"        | Installed directory of DeepMD-Kit 0.x, which should contain `bin lib include`. |
| python_path                        | String               | "....../python3.6/bin/python" | Python path for DeePMD-kit 1.x installed. This option should not be used with `deepmd_path` together. |
| machine                            | Dict                 |                               | Settings of the machine for TASK.                            |
| resources                          | Dict                 |                               | Resources needed for calculation.                            |
| # Followings are keys in resources |                      |                               |                                                              |
| numb_node                          | Integer              | 1                             | Node count required for the job                              |
| task_per_node                      | Integer              | 4                             | Number of CPU cores required                                 |
| numb_gpu                           | Integer              | Integer                       | 4                                                            |
| manual_cuda_devices                | Interger             | 1                             | Used with key "manual_cuda_multiplicity" specify the gpu number |
| manual_cuda_multiplicity           | Interger             | 5                             | Used in 01.model_devi,used with key "manual_cuda_devices" specify the MD program number running on one GPU at the same time,dpgen will automatically allocate MD jobs on different GPU. This can improve GPU usage for GPU like V100. |
| node_cpu                           | Integer              | 4                             | Only for LSF. The number of CPU cores on each node that should be allocated to the job. |
| source_list                        | List of string       | "....../vasp.env"             | Environment needed for certain job. For example, if "env" is in the list, 'source env' will be written in the script. |
| module_list                        | List of string       | [ "Intel/2018", "Anaconda3"]  | For example, If "Intel/2018" is in the list, "module load Intel/2018" will be written in the script. |
| partition                          | String               | "AdminGPU"                    | Partition / queue in which to run the job.                   |
| time_limit                         | String (time format) | 23:00:00                      | Maximal time permitted for the job                           |
| mem_limit                          | Interger             | 16                            | Maximal memory permitted to apply for the job.               |
| with_mpi                           | Boolean              | true                          | Deciding whether to use mpi for calculation. If it's true and machine type is Slurm, "srun" will be prefixed to `command` in the script. |
| qos                                | "string"             | "bigdata"                     | Deciding priority, dependent on particular settings of your HPC. |
| allow_failure                      | Boolean              | false                         | Allow the command to return a non-zero exit code.            |
| # End of resources                 |                      |                               |                                                              |
| command                            | String               | "lmp_serial"                  | Executable path of software, such as `lmp_serial`, `lmp_mpi`and `vasp_gpu`, `vasp_std`, etc. |
| group_size                         | Integer              | 5                             | DP-GEN will put these jobs together in one submitting script. |

 
