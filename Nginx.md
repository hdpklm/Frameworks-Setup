## config reload without restart the service
```sh
nginx -t # test configuration
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

## allow websocket
```sh
nano /etc/nginx/sites-available/default

### add this code inside server 80 or 443, after location / roule
location /<route> {
		proxy_pass             http://web_socket_ips;
		proxy_read_timeout     60;
		proxy_connect_timeout  60;
		proxy_redirect         off;

		# Allow the use of websockets
		proxy_http_version 1.1;
		proxy_set_header Upgrade $http_upgrade;
		proxy_set_header Connection 'upgrade';
		proxy_set_header Host $host;
		proxy_cache_bypass $http_upgrade;
}
```