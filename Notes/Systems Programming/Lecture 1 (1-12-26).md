---
tags:
  - Systems_Programming
---

- This is the **Unix** philosophy (will be on exam 1):
	- write programs that do **one thing** and **do it well**
	- write programs that work **together**
	- write programs that handle **text streams**, because that is a universal interface

- Env command shows all the active variables (used with a pager here)
- ```bash
  Env | less 
  ```
	- press space to navigate to the next page
	- press 'q' to exit page

- The ```env``` command returns a lot of:
	-```variable_name = value```

- By convention shell variables are all CAPS

- the following command is dereferencing the value of the SHELL variable
```bash
echo $SHELL
```

- the following command is assingning cat to the SHELL variable, must be NO SPACES
- ```bash
  SHELL=cat
  ```

- when using man pages on the web make sure it is LINUX man page

```bash
alias va="ls -la"
```
- this aliases "ls-la" as "va"
	- but this doesn't work on a new shell
	- we need to add to config (bashrc) file for it to persist

```bash
source ~/.bashrc
``` 
- this loads the bashrc file again
	- this is helpful so that if you update the config you don't have to restart the terminal to use the change

```bash
cd -
``` 
- this takes me back to where I just was