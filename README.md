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
