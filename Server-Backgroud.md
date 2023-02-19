# Use supervisor

## install
```sh
apt install supervisor -y
```

## configurate
```sh
cd /etc/supervisor/conf.d
nano <app-name>.conf

### insert this code ###
[program:<app_name>]
command=<bash copmmand, use full path for files>
numprocs=1 # numero de procesos
autostart=true
autorestart=true
### end of file ###

supervisorctl reread
supervisorctl update
supervisorctl status
```


