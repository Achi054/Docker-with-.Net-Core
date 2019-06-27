# Docker with .Net Core

## Things to know

    - Working with .Net Core
    - Basics to Docker
        Container is just a process runnig on the background
            - Applciation can have networking stack
            - File system
            - Host name
            - Registry
        Container image are file systems containing files in a different format. It composes of executable file to run on Container cache, that can then be run on container.
        So a Container is a process and the container is the file system for the process.

## Installing Docker

- Download docker from [Docker for Windows](https://docs.docker.com/docker-for-windows/install/)<br/>
  ```
  docker --version
  ```

## Working with Docker Images

- Create a `microsoft/dotnet` image

  ```
  docker run --rm -it microsoft/dotnet:2-runtime dotnet --info
  ```

  The above command runs pulls the `microsoft/dotnet` image, if not already exists and runs `dotnet --info` command<br/>

- Find running containers

  ```
  docker ps

  To find all containers:
  docker ps -a
  or
  docker container ls -a
  ```

  ps - Process status<br/>

- Stop a container

  ```
  docker stop <container-id>
  ```

- Remove a container

  ```
  docker container rm <container-id(s)>
  ```

- Find all images

  ```
  docker images
  ```

- Remove a image

  ```
  docker rmi nginx
  ```

- Save image as .tar file

  ```
  docker save mcr.microsoft.com/windows/servercore/iis -o iis.tar
  ```

- Expose host drive to container
  Right click on the Docker and select `Settings`.<br/>
  Select `Shared Drives`, click on the `C:` drive and `Apply` <br/>
  Run the below command

  ```
  docker run --rm -v c:/Users:/data alpine ls /data
  ```

- Setting up a container for `hello-world`

  ```
  docker run hello-world
  ```

  `hello-world` is a image

- Creating a `nginx` container locally
  Nginx (pronounced "engine-x") is an open source reverse proxy server for HTTP, HTTPS, SMTP, POP3, and IMAP protocols, as well as a load balancer, HTTP cache, and a web server (origin server). The nginx project started with a strong focus on high concurrency, high performance and low memory usage.

  ```
  docker run nginx -p 80:80

  Open link:
  http://localhost:80
  ```

  The above snippet runs `nginx` (Pronouced as engine-x) to extract the image an create a container running on port 80<br/>

## Docker for Windows

- Right click on the Docker and click `Switch to windows container`<br/>
- Run a new image from repository
  ```
  docker run -d -p 8000:80 --name iis mcr.microsoft.com/windows/servercore/iis
  ```
- Run `localhost:80` in browser, you would not be able to.
- Find container IP
  ```
  docker inspect
  ```
  renders container details, check for `Networks\IPaddress`
- Navigate to that IP address in browser

# Hosting website static files

- Volume mount
- Copy into the container file system
- Bake file into image

## Volume mount

- Create a static html file, `user.html`
  ```
  <!DOCTYPE html>
  <html lang="en" xmlns="http://www.w3.org/1999/xhtml">
  <head>
      <meta charset="utf-8" />
      <title>Static Website</title>
  </head>
  <body class="site">
      <h2>Static Website</h2>
  </body>
  ```
- Pull the image for Linx server `nginx` from [dockerhub](https://hub.docker.com/_/nginx)
  ```
  docker pull alpine
  ```
- Run the container
  ```
  docker run -it -p 8080:80 nginx
  ```
  Verify: http://localhost:8080 check nginx should be running
- Push static file from Host(local system location) to container(docker container)
  ```
  docker run -it -p 8080:80 -v .\POC\StaticWebsite:/usr/share/nginx/html nginx
  ```
- Run on browser [Website](`http://localhost:8080/user.html`)

## Copy contents into the container file system

- Running `nginx` as a background process

  ```
  docker run -d -p 8080:80 --name nginx-server nginx
  ```

  `-d` denots running daemon as a background process

- Navigate to container through bash prompt

  ```
  docker exec -it nginx-server bash
  ```

- Exit out and copy from Host to Continer file system

  ```
  docker cp .\POC\StaticWebsite\. nginx-server:/usr/share/nginx/html

  Validate:
  docker exec nginx-server ls usr/share/nginx/html
  ```

## Bake file into image

- Create a image out of the copied content

  ```
  Check container files:
  docker cp .\POC\StaticWebsite\. nginx-server:/usr/share/nginx/html

  Create image:
  docker commit nginx-server user-static:nginx

  Verify:
  docker image ls
  ```

- Run the image to a new contaniner on a different port

  ```
  docker run -d -p 8090:80 --name user-static:server user-static:nginx
  ```

  Verify:
  Open [Website](http://localhost:8090/User.html)

# Docker file

A Dockerfile is a text document that contains all the commands a user could call on the command line to assemble an image. Using docker build users can create an automated build that executes several command-line instructions in succession.

## Creating a docker file

    ```
    FROM nginx
    COPY app /usr/share/nginx/html
    ```

`FROM nginx` command is similar to `docker run -d -p 8080:80 --name nginx-server nginx`<br/><br/>
`COPY app /usr/share/nginx/html` is similar to `docker cp .\POC\StaticWebsite\. nginx-server:/usr/share/nginx/html` <br/>

To run the file use

```
docker build -t static-usr:latest .
```

## Docker file for Windows

- Create a `Dockerfile` with the below content

  ```
  FROM microsoft/iis:nanoserver

  COPY . c:/inetpub/wwwroot
  ```

- Place the file in the path of the host website content
- Run docker file
  ```
  docker build -t iis-usr .
  ```
- Create a container
  ```
  docker run -d -p 8000:80 --name static-usr iis-usr
  ```

## Taging the image

- Tag your image
  ```
  docker tag <image-name>:latest <your-image-name>:latest
  ```

## Push to Docker Hub repository

- Login

  ```
  docker login
  ```

  Provide credentials

- Push to repository
  ```
  docker push <repository-name>/iis-user:latest
  ```

# Docker for Microsoft Server

- Pull the image
  ```
  docker pull microsoft/mssql-server-windows-developer
  ```
- Run the image

  ```
  docker run -d -p 1433:1433 -e sa_password=<sa_password> -e ACCEPT_EULA=Y microsoft/mssql-server-windows-developer
  ```

  -e: Enviroment
  sa_password: User sa password
  ACCEPT_EULA: Confirms acceptance of the end user licensing agreement found

- Working with DB

  ```
  docker exec -it local-db microsoft/mssql-server-windows-developer --user=<user_name> --password=<password>
  ```

- Validate container instance

  ```
  docker logs <process_id>
  ```

- Volumes

  ```
  docker run -d -p 1433:1433 -e sa_password=<sa_password> -e ACCEPT_EULA=Y microsoft/mssql-server-windows-developer -v db:/var/lib/msserver

  docker volume ls
  ```

  Creates a dedicated space to hold data

## Handling Containers and Images

- Stopping all running containers

  ```
  docker stop $(docker ps -aq)
  ```

  Stops all the running containers

- Removing all the containers

  ```
  docker rm -f $(docker ps -aq)
  ```

- Removing all the volumes

  ```
  docker volume rm -f $(docker volumn ls -q)
  ```

- Removing volumes on removal of container
  ```
  docker rm -fv <container-id/container-name>
  ```
