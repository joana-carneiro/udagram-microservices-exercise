# Udagram Image Filtering Microservice

Udagram is a simple cloud application developed alongside the Udacity Cloud Engineering Nanodegree. It allows users to register and log into a web client, post photos to the feed, and process photos using an image filtering microservice.

The project is split into three parts:
1. [The Simple Frontend](/udacity-c3-frontend)
A basic Ionic client web application which consumes the RestAPI Backend. 
2. [The RestAPI Feed Backend](/udacity-c3-restapi-feed), a Node-Express feed microservice.
3. [The RestAPI User Backend](/udacity-c3-restapi-user), a Node-Express user microservice.

## Getting Setup

> _tip_: this frontend is designed to work with the RestAPI backends). It is recommended you stand up the backend first, test using Postman, and then the frontend should integrate.

### Installing Node and NPM
This project depends on Nodejs and Node Package Manager (NPM). Before continuing, you must download and install Node (NPM is included) from [https://nodejs.com/en/download](https://nodejs.org/en/download/).

### Installing Ionic Cli
The Ionic Command Line Interface is required to serve and build the frontend. Instructions for installing the CLI can be found in the [Ionic Framework Docs](https://ionicframework.com/docs/installation/cli).

### Installing project dependencies

This project uses NPM to manage software dependencies. NPM Relies on the package.json file located in the root of this repository. After cloning, open your terminal and run:
```bash
npm install
```
>_tip_: **npm i** is shorthand for **npm install**


### Configure The Backend Endpoint
Ionic uses enviornment files located in `./src/enviornments/enviornment.*.ts` to load configuration variables at runtime. By default `environment.ts` is used for development and `enviornment.prod.ts` is used for produciton. The `apiHost` variable should be set to your server url either locally or in the cloud.

***
### Running the Development Server
Ionic CLI provides an easy to use development server to run and autoreload the frontend. This allows you to make quick changes and see them in real time in your browser. To run the development server, open terminal and run:

```bash
ionic serve
```

Backend feed and user microservices can be tested by executing, for each
```bash
npm install
npm run dev
```
Please note that each, is executed in port 8080 and therefore they cannot run at the same time. Ngix proxy will be configured for forwarding the requests for each service.

For testing feed, can be executed
```bash
curl http://localhost:8080/api/v0/feed/
```

### Docker
```bash
docker build -t joanacarneiro/udagram-frontend
docker build -t joanacarneiro/udagram-reverseproxy
docker build -t joanacarneiro/udagram-restapi-user
docker build -t joanacarneiro/udagram-restapi-feed

docker-compose -f docker-compose-build.yaml build --parallel
docker compose up
```
From this point it is possible to test again the application

It is also needed to push the created images into a repository so to be used by Kubernetes

### Running Kubernetes Orchestration
#### Apply Secrets and Configuration Maps
Configuration Maps are located in file env-configmap.yaml. This file needs to be edited according to the environment variables defined.

To apply the config maps execute commands
```bash
kubectl apply -f ./env-configmap.yaml
```

To check if the configs where created
```bash
kubectl get configmap
```
aws-secret.yaml - contains encripted (base64) path to Database credentials file env-secret.yaml
env-secret.yaml - contains Database username and password encrypted (base 64)

To apply the secrets execute commands
```bash
kubectl apply -f ./aws-secret.yaml
kubectl apply -f ./env-secret.yaml
```

To check if the secrets where created
```bash
kubectl get secrets
```

#### Run Pods
Execute 
```bash
kubectl apply -f frontend-deployment.yaml
kubectl apply -f backend-feed-deployment.yaml
kubectl apply -f backend-user-deployment.yaml
kubectl apply -f reverseproxy-deployment.yaml
```

#### Apply Services
```bash
kubectl apply -f reverseproxy-service.yaml
kubectl apply -f backend-feed-service.yaml
kubectl apply -f backend-user-service.yaml
kubectl apply -f frontend-service.yaml
```

#### Port Forwarding
```bash
kubectl port-forward service/reverseproxy 8080:8080
```

```bash
kubectl port-forward service/frontend 8100:8100
```

