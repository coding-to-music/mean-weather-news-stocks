# MY WEB APP - MEAN STACK [![Build Status](https://travis-ci.org/coding-to-music/mean-weather-news-stocks.svg?branch=master)](https://travis-ci.org/coding-to-music/mean-weather-news-stocks)

https://github.com/coding-to-music/mean-weather-news-stocks

https://mean-weather-news-stocks.herokuapp.com/

By Daniel NGUYEN danmgs https://github.com/danmgs

https://xerox-tour.herokuapp.com/

https://github.com/danmgs/My-weather-app

## A WEB application providing weather, news, financial information.

![alt capture1](https://github.com/danmgs/My-weather-app/blob/master/public/img/screenshot1.JPG)

![alt capture2](https://github.com/danmgs/My-weather-app/blob/master/public/img/screenshot2.JPG)

## Folder Organization

They are 2 main components :

- Backend : a Node.js/Express.js webapi using a mongo database for storage
- Frontend : an angular webapp

```
| -- .env                                 -> Environment variables configuration
| -- docker-compose.yml                   -> Docker
| -- docker-compose-release.yml           -> Docker release version
| -- launch.bat                           -> The launcher of the project

| -- /public/                             -> frontend webapp
     | -- angular.json
     | -- package.json
     | -- Dockerfile
     | -- /src
            | -- /app/                    -> contains views/components/services/shared for website.

| -- /server/                             -> backend webapi
    | -- app.js                           -> backend entry point running expressjs
    | -- Dockerfile
    | -- package.json

```

## Launching the docker version

### Start the dockerized version

You can configure some environment variables in the .env file. Then, you can have a preview of the parameters injected:

```
docker-compose config
```

At the root of the solution, run the command :

```
launch.bat up
```

It will docker-compose to build + run full stack (can take a moment while building).

It will open in the browser:

- the frontend webapp (port 80)
- the backend webapi (port 30001).

### Stop the dockerized version

```
launch.bat down
```

### Run the release version

It uses docker images hosted in Docker Hub Registry (fastest way as images are already built).

They are compatible with for **Linux OS**.

```
docker-compose -f docker-compose-release.yml up
```

|          Docker images          | Docker Repository                                                            | Description                     |
| :-----------------------------: | ---------------------------------------------------------------------------- | ------------------------------- |
|          mongo:latest           | [link](https://hub.docker.com/_/mongo)                                       | Mongo Database (official)       |
| danmgs/weather-app-frontend:1.1 | [link](https://hub.docker.com/repository/docker/danmgs/weather-app-frontend) | Angular WebApp                  |
| danmgs/weather-app-frontend:1.1 | [link](https://hub.docker.com/repository/docker/danmgs/weather-app-frontend) | WebApi using Node.js/Express.js |

### Instructions for deployment in the cloud

- AZURE : create a web app in multi-container mode, use file **docker-compose-release.yml**
- AWS : create a web app via Elastic Beanstalk interface, in multi-container mode, use file **Dockerrun.aws.json**

## Frontend WebApp + Backend WebApi : Walkthrough

You can read the README from folder [public](https://github.com/danmgs/My-weather-app/tree/master/public) or [server](https://github.com/danmgs/My-weather-app/tree/master/server) respectively.

## From the file notes.txt

```java
# Manual steps notes
# Follow instructions in order:

# Create the network
docker network create mynetwork
docker network ls

# Build and run frontend webapp
cd public
docker build -t app-frontend --build-arg NODE_ENV=prod .
docker run --rm -d -p 80:80 --name app-frontend app-frontend

# Build and run mongo
# https://github.com/docker-library/mongo/issues/74
docker volume create --name=mongodata
cd server
docker run -d --name mongo-service -v mongodata:/data/db --network=mynetwork mongo

# Build and run backend server with environment variables settings.
docker build -t app-backend .
docker run -d -p 30001:30001 --name app-backend --network=mynetwork -e NODE_ENV=production -e ENV_SERVER_API_MONGODB_URI=mongodb://mongo-service:27017 -e ENV_SERVER_API_ALLOW_HOSTS=* -e ENV_SERVER_API_PORT=30001 app-backend

# check network -> shows 2 containers are linked: backend app container can reach mongo container.
docker inspect mynetwork

# inspect volume and where it lives.
docker inspect mongodata

# check backend url
http://localhost:30001/api/serverhealth

# check frontend url
http://localhost


# check logs
docker logs <containerId>

# other commands

docker kill mongo-service
docker rm mongo-service

docker kill app-frontend
docker kill mongo-service
docker kill app-backend

docker rm app-frontend
docker rm mongo-service
docker rm app-backend

docker ps

# show environment variables injected
docker-compose config

# docker compose
docker-compose -f docker-compose-release.yml up

```

## Installation:

### GitHub

```java
 nvm install v16
 npm install
 git init
 git add .
 git remote remove origin
 git commit -m "first commit"
 git branch -M main
 git remote add origin git@github.com:coding-to-music/mean-weather-news-stocks.git
 git push -u origin main
```

### Heroku

```java
heroku create mean-weather-news-stockst
```

### Heroku MongoDB Environment Variables

```java
heroku config:set MONGODB_URI="mongodb+srv://<userid>:<password>@cluster0.zadqe.mongodb.net/mean-weather-news-stocks?retryWrites=true&w=majority"
git push heroku
```

```java
DATABASE_PASSWORD=""
YOUR_EMAIL_NAME=""
YOUR_EMAIL_ADDR=""
EMAIL_HOST=""
EMAIL_PORT=""
EMAIL_USERNAME=""
EMAIL_PASSWORD=""
STRIPE_SECRET_KEY=""
JWT_SECRET=""
JWT_COOKIE_EXPIRES_IN=""
JWT_EXPIRES_IN=""

```

### Heroku Buildpack

See this repo for more info about setting up a node/react app on heroku:

https://github.com/mars/heroku-cra-node

```java
heroku buildpacks

heroku buildpacks --help

heroku buildpacks:clear

```

### Notice we are doing a SET and then and ADD

```java
heroku buildpacks:set heroku/nodejs

heroku buildpacks:add mars/create-react-app
```

Output:

```java
Buildpack added. Next release on mean-weather-news-stockst will use:
  1. heroku/nodejs
  2. mars/create-react-app
Run git push heroku main to create a new release using these buildpacks.
```

### Lets try reversing the order

```java
heroku buildpacks:set mars/create-react-app

heroku buildpacks:add heroku/nodejs
```

```java
heroku buildpacks
```

Output:

```java
=== mean-weather-news-stockst Buildpack URLs
1. mars/create-react-app
2. heroku/nodejs
```

### Push to Heroku

```
git push heroku
```

## Error:

```java
2022-03-29T08:06:58.791397+00:00 heroku[web.1]: Starting process with command `npm start`
2022-03-29T08:06:59.934025+00:00 app[web.1]: ls: cannot access '/client/build/static/js/*.js': No such file or directory
2022-03-29T08:06:59.938326+00:00 app[web.1]: Error injecting runtime env: bundle not found '/client/build/static/js/*.js'. See: https://github.com/mars/create-react-app-buildpack/blob/master/README.md#user-content-custom-bundle-location
```

Attempted this:

```java
heroku config:set JS_RUNTIME_TARGET_BUNDLE=/client/build/static/js/*.js
```

## Local Development

Because this app is made of two npm projects, there are two places to run `npm` commands:

1. **Node API server** at the root `./`
1. **React UI** in `react-ui/` directory.

### Run the API server

In a terminal:

```bash
# Initial setup
npm install

# Start the server
npm start
```

#### Install new npm packages for Node

```bash
npm install package-name --save
```

### Run the React UI

The React app is configured to proxy backend requests to the local Node server. (See [`"proxy"` config](react-ui/package.json))

In a separate terminal from the API server, start the UI:

```bash
# Always change directory, first
cd react-ui/

# Initial setup
npm install

# Start the server
npm start
```

#### Install new npm packages for React UI

```bash
# Always change directory, first
cd react-ui/

npm install package-name --save
```
