## config reload without restart the service
```sh
nginx -s reload
```

## reverse-proxy
> in `/etc/nginx/conf.d/default.conf`
> 
> add this lines inside `location / {}`
> 
```
proxy_set_header Host $host;
proxy_set_header X-Real-IP $remote_addr;
proxy_set_header X-Forwarded-Host  $host;
proxy_set_header X-Forwarded-Port  $server_port;  # or $remorte_port
proxy_set_header X-Forwarded-For   $proxy_add_x_forwarded_for;
proxy_set_header X-Forwarded-Proto $scheme;
proxy_pass http://server_ips;
```
