# Dockerizing an Angular app

## A Little HowTo

Dockerizing an Angular app is quite easy.

### Files To Add

In preparation, add the following four files:

- Add a `Dockerfile`

  ```dockerfile
  FROM nginx
  LABEL maintainer="Michael Kaaden <github@kaaden.net>"
  COPY dist/dockerized-app /usr/share/nginx/html
  ```

- Add a `.dockerignore` file to prevent too many files and directories to be added
  to your image

  ```
  .dockerignore
  .editorconfig
  .git
  .gitignore
  .idea
  README.md
  angular.json
  coverage
  e2e
  node_modules
  package.json
  package-lock.json
  src
  tsconfig.json
  tslint.json
  yarn.lock
  ```

- Add a `dockerize.sh` script to automate building the image

  ```bash
  #!/bin/bash
  yarn --prefer-offline --no-progress
  ng build --prod
  docker build -t dockerized-app .
  ```

- Add a `docker-compose.yml` to be able to use `docker-compose`

  ```yaml
  version: "3"

  services:
    web:
      image: dockerized-app
      ports:
        - "8093:80"
  ```

- Add a `redeploy.sh` script to automate (re-)starting the container

  ```bash
  #! /bin/bash
  export COMPOSE_HTTP_TIMEOUT=300
  docker-compose down --remove-orphans
  docker-compose up -d
  ```

### Building And Running The Dockerized App

Now it's very easy to build and run the app with Docker:

1. Build the app and the image with `./dockerize.sh`

   Example run:

   ```
   $ ./dockerize.sh
   yarn install v1.13.0
   [1/4] 🔍  Resolving packages...
   success Already up-to-date.
   ✨  Done in 0.41s.

   Date: 2019-03-09T14:56:24.367Z
   Hash: e6105fbbd24ce43b0f57
   Time: 10178ms
   chunk {0} runtime.a5dd35324ddfd942bef1.js (runtime) 1.41 kB [entry] [rendered]
   chunk {1} es2015-polyfills.358ed1827c991dd2afb0.js (es2015-polyfills) 56.4 kB [initial] [rendered]
   chunk {2} main.e87fb3df99e6b4b142c4.js (main) 239 kB [initial] [rendered]
   chunk {3} polyfills.407a467dedb63cfdd103.js (polyfills) 41 kB [initial] [rendered]
   chunk {4} styles.3ff695c00d717f2d2a11.css (styles) 0 bytes [initial] [rendered]

   Sending build context to Docker daemon  390.1kB
   Step 1/3 : FROM nginx
   ---> 42b4762643dc
   Step 2/3 : LABEL maintainer="Michael Kaaden <github@kaaden.net>"
   ---> Using cache
   ---> cceda606119e
   Step 3/3 : COPY dist/dockerized-app /usr/share/nginx/html
   ---> Using cache
   ---> c36c5b91dd00
   Successfully built c36c5b91dd00
   Successfully tagged dockerized-app:latest
   ```

2. Start the container with `./redeploy.sh`

   Example run:

   ```
   $ ./redeploy.sh
   Stopping dockerized-app_web_1 ... done
   Removing dockerized-app_web_1 ... done
   Removing network dockerized-app_default
   Creating network "dockerized-app_default" with the default driver
   Creating dockerized-app_web_1 ... done
   ```

3. Visit `http://localhost:8093` (that's the port defined in the `docker-compose.yml` file)

## Generic @angular/cli README.md contents

This project was generated with [Angular CLI](https://github.com/angular/angular-cli) version 7.3.5.

## Development server

Run `ng serve` for a dev server. Navigate to `http://localhost:4200/`. The app will automatically reload if you change any of the source files.

## Code scaffolding

Run `ng generate component component-name` to generate a new component. You can also use `ng generate directive|pipe|service|class|guard|interface|enum|module`.

## Build

Run `ng build` to build the project. The build artifacts will be stored in the `dist/` directory. Use the `--prod` flag for a production build.

## Running unit tests

Run `ng test` to execute the unit tests via [Karma](https://karma-runner.github.io).

## Running end-to-end tests

Run `ng e2e` to execute the end-to-end tests via [Protractor](http://www.protractortest.org/).

## Further help

To get more help on the Angular CLI use `ng help` or go check out the [Angular CLI README](https://github.com/angular/angular-cli/blob/master/README.md).
