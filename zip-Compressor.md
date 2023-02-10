
> compres folder into foldername.zip
```sh
zip -r foldername.zip /path/to/folder1
```

> list files in zip file
```sh
unzip -l files.zip 
unzip -l files.zip | less
### if there are too many files to be listed in one page
```

> delete file deom zip file
```sh
zip  -d  file.zip  from/path/file.txt
zip  -d  file.zip  from/path/\*  \*.o
### this will delete all files in from/path/ and all ended with ".o"
```

