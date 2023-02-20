## run 2 service in one container
```dockerFile
FROM ubuntu

RUN apt-get update
RUN apt-get install -y apache2
RUN apt-get install -y mongod #pretend this exists
RUN apt-get install -y supervisor # Installing supervisord

ADD supervisord.conf /etc/supervisor/conf.d/supervisord.conf 

EXPOSE 80

ENTRYPOINT ["/usr/bin/supervisord"]
```

> in docker dir: create this file "supervisord.conf"
```conf
[supervisord]
nodaemon=true

[program:mongodb]
command=/etc/mongod/mongo; command to service 1

[program:apache2]
command=/usr/sbin/apache2 -DFOREGROUND; command to service 2
```


