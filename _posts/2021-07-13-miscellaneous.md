---
layout: post
title: Miscellaneous
---

This is a constantly updating post where I share my experience of encountering bugs, in like baisc linux system, or comutational packages.

## Calculation submission

- make sure the submission scripts / setup files have the right **sytax**.
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