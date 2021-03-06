# Command
- git fetch
- git merge [branch]
- git remote
- git add .
- git add -A
- git add -u
- git status
- git diff
- git commit -m "msg"
- git cherry-pick [hashcode]

# Issue
- After added ssh key to github, still need to provide username and password when I did push operation.

```
Logon failed, use ctrl+c to cancel basic credential prompt.
Username for 'https://github.com':
remote: Anonymous access to huangshijie/Note.git denied.
fatal: Authentication failed for 'https://github.com/huangshijie/Note.git/'
```
From error log above, we can found out local git still do authorization in git's  **https** way. 
We can use ssh of repo to replace https of repo to change paramter **remote** in file /.git/config.

```
remote = git@github.com:******
```

---

- First time clone git from github, then push code to this repo
  
```
C:\Git\bin\git.exe push --recurse-submodules=check --progress "origin" refs/heads/master:refs/heads/master
Fatal: HttpRequestException encountered.
bash: /dev/tty: No such device or address
error: failed to execute prompt script (exit code 1)
fatal: could not read Username for 'https://github.com': Invalid argument
Done
```

```
Update git version
```

---

![Git Command](https://github.com/huangshijie/ImgRep/blob/master/Git%2Bcheat%2Bsheet.jpg)