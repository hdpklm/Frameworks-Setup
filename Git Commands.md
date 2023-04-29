# Github

## <ins>configure and save **user** and **password**
```sh
git config --global credential.helper store

# only the first time ask for credential, after push or pull
git pull
```
---
## <ins>create new repository</ins>
### initialize repo
#### at first you have to create the repository in github or pass the token with url
#### buy I prefere to not share the token
```sh
git init
```
### add all files to repo
```sh
git add .
```
### commit the firt changes
```sh
git commit -m "First commit"
```
### add the origin url
```sh
git remote add origin https://github.com/[user]/[repo]
```
### send all data to server
```sh
git push -u -f origin master
```
---


## <ins>Commits</ins>
### Delete  commits
```sh
git reset --hard [restore hash. ex:71c27777543ccfcb0376dcdd8f6777df055ef479]
git push --force
```

## <ins>Remove changes</ins>
> in file
```sh
git checkout -- <file>
```
> all changes
```sh
git clean -df
git checkout -- .
```

## <ins>Sync branches</ins>
```sh
git fetch -p
```
  
## <ins>Merge branch</ins>
```sh
git checkout master		# master is checked out
git pull			# update local
git merge new-feature		# merge branch new-feature into master
git push			# changes on remote. Then checkout a feature branch
```

## <ins>get current hash</ins>
```sh
git rev-parse --short HEAD
```

## show last commit
```sh
git show --summary
```

## <ins>Tags</ins>
### create
```sh
git tag -a [name] -m [message] ([hash] to link to older hash)
```

### sync
```sh
git push origin --tags
```

### Delete
```sh
git tag -d [name]
```
