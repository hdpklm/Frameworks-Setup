## Commits
### Delete  commits
```
git reset --hard [restore hash. ex:71c27777543ccfcb0376dcdd8f6777df055ef479]
git push --force
```

## Sync branches
```sh
git fetch -p
```

## Tags
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
