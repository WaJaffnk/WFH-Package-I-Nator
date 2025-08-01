# WFH NPM Package Server

## Overview
Built by example as seen here:
https://github.com/WaJaffnk/verdaccio/tree/master/docker-examples/v6/docker-local-storage-volume

This application serves as a local registry for NPM packages, but also as a proxy, enabling NPM to check here FIRST, and then if not found - actual NPM registry.

## SETUP
Before starting the app - you will need to create a file in the conf folder called `./conf/htpasswd`.  This file will allow you to setup a user in order to 

```bash
npm adduser --registry http://0.0.0.0:4873/
```

output will look something like this: 
```
npm notice Log in on http://0.0.0.0:4873/
Username: jeff
Password: 
Password: 

⠇
Email: (this IS public) 
⠹
```

You will also need to set the registry to be available at a global level for NPM.  Do not worry about this interrupting typical NPM usage though, verdaccio will actually act as a proxy as well.  If you'd like to NOT set as the primary registy - you can use the flag (see Running the APP below)
```bash
npm set registry http://0.0.0.0:4873/
```

## Running the app

command to run app:
`docker compose up`

Once you make changes to this file - you will need to publish the new application.  NOTE - you will need to change the version found in the `package.json`
```bash
npm publish 
```

After the desired package is published - the package can be installed (via normal means - including Docker/Scripts/etc...) using the same command:
```bash
npm install
```
(Note: this will utilize the registry once established above.) 

You can also pass the registry with a flag - allowing you to keep npmjs as the primary resource. 
```bash
npm install --registry http://localhost:4873
```

## Trouble Shooting

If you need to connect to a Docker container that is consuming this as an endpoint - you may need to update the npm config to point to the localhost registry while building the image. example:
```Dockerfile
RUN npm config set registry http://localhost:4873

RUN npm install

#etc...
```

To connect to a file directly - you may need to override the packages `entry point` (ie: start point for the app). You can use the `entrypoint` flag:
```bash
docker run --entrypoint /bin/sh -it logging-service
```