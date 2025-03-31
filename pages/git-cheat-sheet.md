## Here's a tutorial for setting up SSH in visual studio

[ssh on vs code](https://code.visualstudio.com/docs/remote/ssh-tutorial)
	
## SSH

When you want to `ssh` into the raspberry pi you will need to know the IP address. It will look like this only swap in whatever IP address the pi. Open a terminal and type
```
ssh izzymones@192.168.0.100
```

## Turn on your python virtual environment (venv)
Once in the code directory type
```
source venv/bin/activate
```


## [Git cheat sheet](https://ndpsoftware.com/git-cheatsheet.html#loc=index;)

## Get changes 
If we have multiple people working on the code, it's good to start a coding session with a `pull`. 
```
git pull
```
This will update your local copy of the code with any changes that were checked in to the repo by someone else.

## Checking in your changes

```
git status
git add -u
git commit -m "this is what I changed"
git push
```

This is what I typically run when I'm checking in code. `git status` tells me what files I've changed. `git add -u` tells git to _stage_ the changes and the `git commit` file commits the changes locally. `git push` send the changes back to your github page repository on line.

### git add

```
git add my-file.tsx
```

Adds the file `my-file.tsx` to the project. Once you add the file you still need to run `git add -u`, `git commit` and `git push`.

## Getting code from the repository

When you want to get the changes that others have checked in to the repository you run

```
git pull
```
