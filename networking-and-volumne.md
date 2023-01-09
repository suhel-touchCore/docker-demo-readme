# Cheat Sheet

```bash
docker version
docker ps
docker network ls
docker container run -dt - -name con1 alpine
docker container run -dt - -name con2 alpine
docker inspect con1
docker inspect con2
docker exec -it con1 /bin/sh
```

## Exercise 1: Create New Bridge Network

### Scenario 1: Create Bridge
```bash
docker network create --driver bridge tcsbridge
docker container run -dt --name con3 --network tcsbridge alpine
docker container run -dt --name con4 --network tcsbridge alpine
docker exec -it con4 /bin/sh
```

### Scenario 2: Create Bridge
```bash
docker network create --driver bridge --subnet 172.43.0.0/16 tcsbridge1
```

### Example 3: Attached and detached networks on the fly
```bash
docker network connect tcsbridge1 con1
```

## Exercise 2: Docker Container Layer

### Scenario 1: Manual approach 
```bash
docker container run -dt --name c1 ubuntu
docker exec -it c1 /bin/bash
apt update && apt install nginx
```
 
### Scenario 2: Create Custom Image, try below things on localhost
```bash
mkdir demo
cd demo
echo “Welcome To TCS Demo” > /var/www/html/index.nginx-debian.html
```

### Create Dockerfile with below instruction
```Dockerfile
FROM ubuntu
RUN apt update && apt install nginx
COPY index.nginx-debian.html /var/www/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"] 
docker build -t nginx:v1 .
docker history nginx:v1
```

## Exercise 3: Docker Volume and Layer

### Scenario 1: Data is not persist into container
```bash
docker container run -dt --name c1 alpine
docker exec -it c1 /bin/bash
create file
docker container rm c1 –f
```

### Scenario 2: Create Volume
```bash
docker volume create tcsvolume
docker volume ls
docker container run -dt --name c1 -v tcsvolume: /var/www/html nginx:v1
```
### Scenario 3: Another way to create volume is using docker file
```Dockerfile
FROM ubuntu
RUN mkdir /myvolume
RUN echo  “Another way to create volume” > /myvolume/hello.txt
VOLUME /myvolume
```
### Scenario 4: Persist data using docker commit
```bash
docker container run -dt --name c1 alpine
docker exec -it c1 sh
mkdir demo
cd demo
echo "Test File" >> test.txt
docker container commit c1 c2
docker container run -dt --name c2 c2
docker exec -it c2 /bin/sh
```
