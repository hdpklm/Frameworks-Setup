# Github

## <ins>create new repository</ins>
### initialize repo
#### at first you have to create the repository in github or pass the token with url
#### buy I prefere to not share the token
```
git init
```
### add all files to repo
```
git add .
```
### commit the firt changes
```
git commit -m "First commit"
```
### add the origin url
```
git remote add origin https://github.com/[user]/[repo]
```
### send all data to server
```
git push -u -f origin master
```
---


## <ins>Commits</ins>
### Delete  commits
```
git reset --hard [restore hash. ex:71c27777543ccfcb0376dcdd8f6777df055ef479]
git push --force
```

## <ins>Sync branches</ins>
```sh
git fetch -p
```

## <ins>Tags</ins>
### create
```
git tag [name] -m [message] ([hash] to link to older hash)
```

### sync
```
git push origin --tags
```

### Delete
```
git tag -d [name]
```
