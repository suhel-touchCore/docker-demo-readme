# Introduction to Docker

## Basic Commands

### List all the docker images
```bash
docker images
```

### List docker containers
```bash
docker ps
```

### List all the docker (including the stopped once)
```bash
docker ps -a
```

### Building a Docker image from Docker file
```bash
docker build -t <image-name> <dockerfile-path>
```

### Running a Docker container from Docker image
```bash
docker run -it --name <container-name> <image-name>
```

### Cloning the GitHub Repository
```bash
git clone https://github.com/SuhelMakkad/angular-todo-app.git
cd angular-todo-app
```

## Building an Angular App with Docker

### Importing the ubuntu image
```Dockerfile
FROM ubuntu
```

### Installing curl
```Dockerfile
RUN apt-get update
RUN apt-get -y install curl
```

### Installing nvm
```Dockerfile
ENV NVM_DIR /usr/local/nvm
ENV NODE_VERSION 18.13.0
ENV NODE_PATH $NVM_DIR/v$NODE_VERSION/lib/node_modules
ENV PATH $NVM_DIR/versions/node/v$NODE_VERSION/bin:$PATH

RUN mkdir /usr/local/nvm
RUN curl https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash \
    && . $NVM_DIR/nvm.sh \
    && nvm install $NODE_VERSION \
    && nvm alias default $NODE_VERSION \
    && nvm use default
```

### Getting the code to docker image
```Dockerfile
RUN mkdir app
WORKDIR /app

COPY . .
```

### Building the app
```Dockerfile
RUN npm ci
RUN npm run build
```

### Final Dockerfile
```Dockerfile
FROM ubuntu

RUN apt-get update
RUN apt-get -y install curl

# stting up nvm
ENV NVM_DIR /usr/local/nvm
ENV NODE_VERSION 18.13.0
ENV NODE_PATH $NVM_DIR/v$NODE_VERSION/lib/node_modules
ENV PATH $NVM_DIR/versions/node/v$NODE_VERSION/bin:$PATH

RUN mkdir /usr/local/nvm
RUN curl https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash \
    && . $NVM_DIR/nvm.sh \
    && nvm install $NODE_VERSION \
    && nvm alias default $NODE_VERSION \
    && nvm use default

RUN mkdir app
WORKDIR /app

COPY . .

RUN npm ci
RUN npm run build
```

## Building the Angular App with Per-Build Node Image

### Importing the node image
```Dockerfile
FROM node:19-alpine3.16
```

### Copying the code and build the app
```Dockerfile
RUN mkdir app
WORKDIR /app

COPY . .

RUN npm ci
RUN npm run build
```
### Final Dockerfile
```Dockerfile
FROM node:19-alpine3.16

RUN mkdir app
WORKDIR /app

COPY . .

RUN npm ci
RUN npm run build
```

### Hosting the Angular App with Nginx
```Dockerfile
# build stage
FROM node:19-alpine3.16 as builder

RUN mkdir app
WORKDIR /app

COPY . .

RUN npm ci
RUN npm run build

# hosting stage
FROM nginx:1.23.3-alpine-slim
COPY --from=builder /app/dist/angular-todo-app /usr/share/nginx/html
# needed for Single Page Apps
COPY /nginx.conf /etc/nginx/conf.d/default.conf 
```

### Exposing the port to access the app
```bash
docker run -it --name angular-app -p 8081:80 angular-todo-app
```
