# GeneGinie Project

[Project Overview and architecture information](https://drive.google.com/file/d/1LLVk6F5CWMrpBzZxysWPCxlLPgrhoW2i/view?usp=sharing) 

# GeneWebappFrontend

This repository holds the code for the Angular Frontend dashboard.
The Frontend was generated with [Angular CLI](https://github.com/angular/angular-cli) version 13.0.0.

## Basics and Requirements


```
Angular CLI: 13.0.4
Node: 16.13.2
Package Manager: npm 8.1.2
OS: darwin arm64

Package                         Version
---------------------------------------------------------
@angular-devkit/architect       0.1300.4
@angular-devkit/build-angular   13.0.4
@angular-devkit/core            13.0.4
@angular-devkit/schematics      13.0.4
@angular/cdk                    13.2.2
@angular/cli                    13.0.4
@angular/material               13.2.2
@schematics/angular             13.0.4
rxjs                            7.4.0
typescript                      4.4.4
```
## Setup

Run npm install (inside project folder.)

## Development server 

1. If angular is installed globally with  `npm install -g @angular/cli`
Run `ng serve` for a dev server.

2. If angular is installed locally run the development server using npx
`npx ng serve`

3. If a port is not specified by default it will use 4200
4. Once the server is running, make sure that GeneGenieWebAppBackend server is also running and go to http://localhost:4200/home in your browser of choice.

## Build

Run `ng build` to build the project. The build artifacts will be stored in the `dist/` directory.

## Deployment Information

(TODO) DOCKER INFO AND SETUP

1. CREATE Dockerfile (Above project root)

```

FROM node:16-alpine as builder
WORKDIR /gene-webapp-frontend

COPY /gene-webapp-frontend/package.json .
COPY /gene-webapp-frontend/package-lock.json .

RUN npm install
COPY /gene-webapp-frontend .

##Only for debug
#RUN apk update && apk add bash
#ENTRYPOINT ["/bin/bash"]

RUN npm run ng build
FROM nginx:alpine
WORKDIR /usr/share/nginx/html
RUN rm -rf ./*
COPY --from=builder /gene-webapp-frontend/dist/gene-webapp-frontend .

## Remove the default nginx.conf
RUN rm /etc/nginx/conf.d/default.conf

## Replace with our own nginx.conf
COPY nginx.conf /etc/nginx/conf.d/

##Export port 80 that is the one that is used by nginx
EXPOSE 80
ENTRYPOINT ["nginx","-g","daemon off;"]

```

2. CREATE .dockerignore (Above project root)
```
gene-webapp-frontend/node_modules
node_modules
```

3. Create nginx.conf (Above project root)
```
server {
  listen 80;
  sendfile on;
  default_type application/octet-stream;

  gzip on;
  gzip_http_version 1.1;
  gzip_disable      "MSIE [1-6]\.";
  gzip_min_length   256;
  gzip_vary         on;
  gzip_proxied      expired no-cache no-store private auth;
  gzip_types        text/plain text/css application/json application/javascript application/x-javascript text/xml application/xml application/xml+rss text/javascript;
  gzip_comp_level   9;

  root /usr/share/nginx/html;

  location / {
    try_files $uri $uri/ /index.html =404;
  }
}
```

## Running end-to-end tests

Run `ng e2e` to execute the end-to-end tests via a platform of your choice. To use this command, you need to first add a package that implements end-to-end testing capabilities.

## Further help

To get more help on the Angular CLI use `ng help` or go check out the [Angular CLI Overview and Command Reference](https://angular.io/cli) page.
