# Create Docker image

There are two method for create docker image.

## First method (default)

Create a file into the project root directory and given the file name **_Dockerfile_** without extension. Open dockerfile and write bellow the code.

```doc
WORKDIR /src/app
COPY package*.json ./
EXPOSE 3000

RUN npm ci
COPY . /src/app
CMD ["npm", "start"]
```

**Note :** copy destination must be same with **workdir**(**e.g.** COPY . <WORKDIR>)\
Now run bellow command step by step by using `docker CLI`

- buid with docker

```doc
docker build . -t <your docker username>/<app-name>:<tag>
```

OR

```doc
docker build . -t <app-name>:<tag>
```

- image run\
  -p flag redirects a public port to a private port inside the container\
  -d runs the container in detached mode, leaving the container running in the background

```doc
docker run -p 3001:3000 -d  <your docker username>/<app-name>:<tag>
```

OR

```doc
docker run --name <new image name> -d -p 3001:3000 <your docker username>/<app-name>:<tag>
```

OR

```doc
docker run --name <new image name> -d -p 3001:3000 <app-name>
```

## Second method (docker-compose)

Create a file into the project root directory and given the file name **_Dockerfile_** without extension. Open dockerfile and write bellow the code.

```doc
FROM node:14-alpine as base

WORKDIR /src/app
COPY package*.json ./
EXPOSE 3000

FROM base as production
ENV NODE_ENV=production
RUN npm ci
COPY . /
CMD ["node", "bin/www"]

FROM base as dev
ENV NODE_ENV=development
RUN npm install -g nodemon && npm install
COPY . /
CMD ["nodemon", "bin/www"]
```

Now create a file into the project root directory and given the file name **_docker-compose.yml_**. Open dockerfile and write bellow the code.

```doc
version: '3.8'
services:
  web:
    build:
      context: ./
      target: production
    volumes:
      - .:/src/app
    command: npm run start
    ports:
      - '3000:3000'
    environment:
      NODE_ENV: production
      DEBUG: nodejs-docker-express:*
```

Now run bellow command step by step by using `docker CLI` for method second.

- docker compose command\
  `docker-compose build`\
  `docker-compose up`
- doing frequent changes to Dockerfile for testing then use\
  `docker-compose up --build`
- This removes all volumes before starting the containers again with (docker-compose up).\
  `docker-compose down -v`

**Note :** Both method are need add .dockerignore file and open .dockerignore and write bellow the code.

```doc
.dockerignore
node_modules
npm-debug.log
Dockerfile
.git
.gitignore
.npmrc
```

# Other docker command

- show docker image list\
  `docker images`
- get container ID\
  `docker ps`
- remove unuse image\
  `docker ps -a`
- print app output\
  `docker logs <container id>`

## License

[MIT](https://choosealicense.com/licenses/mit/)
