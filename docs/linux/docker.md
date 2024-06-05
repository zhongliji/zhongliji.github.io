## docker install
```
wget https://download.docker.com/linux/centos/docker-ce.repo

yum -y install docker-ce
systemctl enable docker
systemctl start docker

docker version
docker images
docker ps 
docker ps -a
docker save c7:mfs > xxx.tar
docker load < xxx.tar
docker rm -f xxx
docker rmi xxx
docker history c7:mfs
docker logs xxx

docker stop $(docker ps -a -q) #stop all container
docker rm $(docker ps -a -q) #remove all container
docker rmi $(docker images -q) #remove all image
```
## modify docker root dir
```
vi /usr/lib/systemd/system/docker.service
ExecStart=/usr/bin/dockerd --graph /data/docker
systemctl daemon-reload
systemctl restart docker
docker info
```
## config docker network
```
docker network create li --subnet=192.168.10.0/24 #default bridge
docker network create li3 -d overlay --subnet=192.168.11.0/24
docker network create li2 -d macvlan --subnet=192.168.20.0/24 --ip-range=192.168.20.50/30 --gateway=192.168.20.1 -o parent=eth0
docker network ls
docker network inspect li
```
## create container
```
docker run -d --network liyang --ip 192.168.10.11 -h n11 --name n11 --privileged=true c7:mfs

docker run -p 81:80 -v /data:/data -d --network liyang --ip 192.168.10.11 -h n11 --name n11 --restart=always --privileged=true c7:mfs

for i in {11..12};do docker run -d --network liyang --ip 192.168.10.$i -h n$i --name n$i --privileged=true c7:mfs;done

docker run -d -p 88:22 -p 139:139 -p 445:445 -p 80:80 -v /share:/share --network liyang --ip=192.168.30.100 -h n11 --name n11 --privileged=true c7:mfs
```