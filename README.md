#### pkdevmicroservnode

#####7 Monitering
register
```
https://keymetrics.io/pricing/
```
port 80:43554
```
pm2 install pm2-server-monit
```


#####8 Deploying

######Deployments with PM2
ecosystem:
```
pm2 ecosystem
```
gen ssh first:
```
ssh-keygen -t rsa
```
Enter file in which to save the key:
```
/Users/ke/.ssh/pm2_rsa
```
```
cat pm2_rsa.pub | ssh u@server 'cat >> .ssh/authorized_keys'
```
on local machine,add
```
ssh-add pm2_rsa
```
then deploy
```
pm2 deploy ecosystem.json production setup
```
######Docker a container
search centos with 1000 star or more
```
docker search -s 1000 centos
```

set up a customer image using centos
```
docker pull centos
docker run -it centos /bin/bash
curl --silent --location https://rpm.nodesource.com/setup_5.x | bash -
yum -y install nodejs
yum install -y gcc-c++ make
docker commit -a rengokantai 123454 imagename:latest
```

run this container
```
docker run -it imagename:latest /bin/bash
```
run:
```
docker run -it -v /local:/container  -p 8000:3000 imagename:latest /bin/bash
```

######Load balancing
basic round robin way(every node get same requests
```
http{
  upstream app{
    server 10.0.0.1:3000;
    server 10.0.0.2:3000;
  }
  server {
    listen 80;
    location / {
      proxy_pass: http://app;
    }
  }
```
least_conn: request to least connected node
```
  upstream app{
    least_conn;
    server 10.0.0.1:3000;
    server 10.0.0.2:3000;
  }
```
ip_hash:
```
  upstream app{
    ip_hash;
    server 10.0.0.1:3000;
    server 10.0.0.2:3000;
  }
```
weight:
```
  upstream app{
    server 10.0.0.1:3000 weight=5;
    server 10.0.0.2:3000;
  }
```
reload
```
sudo /etc/init.d/nginx reload
```
active health check (*)
```
http{
  upstream app{
    zone app test;
    server 10.0.0.1:3000;
    server 10.0.0.2:3000;
  }
  server {
    listen 80;
    location / {
      proxy_pass: http://app;
      health_check;
    }
  }
```
