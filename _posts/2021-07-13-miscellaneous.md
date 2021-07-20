---
layout: post
title: Miscellaneous
---

This is a constantly updating post where I share my experience of encountering bugs, in like baisc linux system, or comutational packages.

## Calculation submission

- make sure the submission scripts / setup files have the right **syntax**.
- `dpgen` usage: If a input `*.npy` file is invalid, go to the tmp file and you will see something like

  > Subcommand needs root privillage
  
  This doesn't say any thing about where is wrong. Need to go to each submitted job to check the stderr/stdout file.

- If a dpgen job has reshape problem, that means the dimension of initial set is not properly propared
  
  - Using `np.load(<file_path>)` to check the dimension. DeBug.

  - Re-run your initializing script to generate new initial dataset

  - **Remove the all files in the tmp calculation path, because the way this submission works is copying the initial data to this temporary directory. Even if you have fixed the dimension discrepancy, re-run dpgen will not update these inital dataset files!!** Be careful.

    ```
    # This is what you will see in the log if you restart dpgen
    # It's clearly that dpgen didn't bother to updata this submission after you changed the inital dataset
    # If you had dimension problem in you inital data
    # A re-run will not overwrite the initial data in the calculation directory
    
    INFO:dpgen:restart from old submission a1537a06-b570-47c9-b95b-eea7285a011c for chunk 8aefb06c426e07a0a671a1e2488b4858d694a730
		INFO:dpgen:restart from old submission beb8b0de-6be0-41bf-941b-27cd27afc617 for chunk e193a01ecf8d30ad0affefd332ce934e32ffce72
		INFO:dpgen:restart from old submission 6d5420b5-bc68-42e8-bd9c-d2d3e37ca639 for chunk 6fc978af728d43c59faa400d5f6e0471ac850d4c
		INFO:dpgen:restart from old submission 887cdfab-7fca-43c6-a7a2-3e7f8a62af30 for chunk 221407c03ae5c73109cce71d27e24637824f3333
    ```

## Linux related

### ssh

- more about `ssh-keygen` (**changing passphrase**):
  - Command `ssh-keygen -f ~/.ssh/<id_rsa>` can change the passphrase you generated you private key and public keys. After excuting this command, you are required to write your old passphrase once and then the new one twice.
  - The passphrase is **only** related to your private key file, thus change this passphrase will not affect the public key `<id_rsa>.pub`

## Python related

### `KeyError` in python: 

First, you are supposedly already familiar with the concept of **key**. In database, we have concepts like "super key", "primary key", "foreign ket" ... Keys in database are used to identify a paticular row or column.  While in python dictionary, a key is an *element* each has an *value* (char/string, number, array) assigned to that.

For example, 

```json
{
	"_comment": "This is an example *.json file"
  "book": [
  	{
  	  "id":"01",
      "language": "Java",
      "edition": "third",
      "author": "Herbert Schildt"
    },
    {
      "id":"07",
      "language": "C++",
      "edition": "second",
      "author": "E.Balagurusamy"
    }
  ]
}
```

`"book"` is a key s.t. it has 2 values assigned to it. Python reads `*.json` into a python dictionary, and the key in `*.json` file is exactly the key in python dictionary.

- I encountered KeyError: 'numb_models' and there is a python script thats read two `.json` files, however It didn't find key 'numb_models'.  Thats because
  
  ```shell
  <pyscr>.py json1 json2 <---- the right one
  <pyscr>.py json2 json1 <---- error
  ```

## TODO

- [ ] Constantly adding items in this file 