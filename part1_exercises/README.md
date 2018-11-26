Rows with **$** signify a command on the command line. Code and command line output are usually inside a code block.

## Exercise 1.1
First I create 3 (named) nginx containers
```
$ docker run --name nginx1 -d nginx
$ docker run --name nginx2 -d nginx
$ docker run --name nginx3 -d nginx
```
Then I see all of them are running
```
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
95db0039cdb6        nginx               "nginx -g 'daemon of…"   3 seconds ago       Up 3 seconds        80/tcp              nginx3
b75be34b9796        nginx               "nginx -g 'daemon of…"   8 seconds ago       Up 7 seconds        80/tcp              nginx2
0e551c632ff9        nginx               "nginx -g 'daemon of…"   15 seconds ago      Up 14 seconds       80/tcp              nginx1
```
Next I stop 2 of the 3
```
$ docker stop nginx1
nginx1

$ docker stop nginx2
nginx2
```
Once again, check the status. One is running, 2 are stopped.
```
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                     PORTS               NAMES
95db0039cdb6        nginx               "nginx -g 'daemon of…"   33 seconds ago      Up 33 seconds              80/tcp              nginx3
b75be34b9796        nginx               "nginx -g 'daemon of…"   38 seconds ago      Exited (0) 4 seconds ago                       nginx2
0e551c632ff9        nginx               "nginx -g 'daemon of…"   45 seconds ago      Exited (0) 7 seconds ago                       nginx1
```
Done.

## Exercise 1.2
You can see the original status of 
```
$ docker ps -a
```
from the end of the previous exercise, and the starting point of
```
$ docker images
```
from the next code snippet.

```
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
nginx               latest              e81eb098537d        4 days ago          109MB
```
Let's remove the first two containers:
```
$ docker rm nginx1
nginx1

$ docker rm nginx2
nginx2
```
I know that the third one is still running, so I have to stop it (or use **force**) before I can remove it.
```
$ docker stop nginx3
nginx3

$ docker rm nginx3
nginx3
```
And then I check the status of the containers:
```
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
```
None left, so I can remove the image.
```
$ docker rmi nginx
Untagged: nginx:latest
Untagged: nginx@sha256:31b8e90a349d1fce7621f5a5a08e4fc519b634f7d3feb09d53fac9b12aa4d991
Deleted: sha256:e81eb098537d6c4a75438eacc6a2ed94af74ca168076f719f3a0558bd24d646a
Deleted: sha256:7055505a92c39c6f943403d54a1cda020bfeb523b55d9d78bfe1dad0dd32bb2d
Deleted: sha256:378a8fcc106dc4d3a9f2dc0b642b164e25de3aab98a829e72b2d8c0cf0bad8ee
Deleted: sha256:ef68f6734aa485edf13a8509fe60e4272428deaf63f446a441b79d47fc5d17d3
```
Next I can check that there are no images left
```
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
```

## Exercise 1.3 - The way of the early material
As the docker image I are using (Ubuntu 16.04) does not have curl installed beforehand, I have to install it. To get the installation running, I have to update apt-get repositories first, then install curl.
```
$ docker run -d --rm -it --name curler ubuntu:16.04 sh -c 'read website; sleep 3; curl http://$website'
```
To get inside the container and install curl, I use following commands
```
$ docker exec -it curler bash

$ apt-get update
$ apt-get install -y curl
$ exit
```
With the last command I exit to my own shell. Here I type
```
$ docker attach curler
```
To use the container for the weburl-curl. Here I typed *google.com* to get
```
$ google.com
<HTML><HEAD><meta http-equiv="content-type" content="text/html;charset=utf-8">
<TITLE>301 Moved</TITLE></HEAD><BODY>
<H1>301 Moved</H1>
The document has moved
<A HREF="http://www.google.com/">here</A>.
</BODY></HTML>
```
The option *--rm* took care of the container and removed it after it closed after the curl, and also of course exited me from the container, as it did not exist anymore.

## Exercise 1.3 - The way of the material later on
I start by creating a Dockerfile, in which I have the curl installed.
```
FROM ubuntu:16.04

WORKDIR /homie
RUN apt-get update && apt-get install -y curl
```
Then I run following to have my own image with Ubuntu with curl:
```
$ docker build -t ubucurl .
```
And to finally run the container, I run
```
$ docker run -d --rm -it --name ubucurler ubucurl sh -c 'read website; sleep 3; curl http://$website'
```
And attach to it for the curling part
```
$ docker attach ubucurler
```
Here I typed *google.com* to get
```
$ google.com
<HTML><HEAD><meta http-equiv="content-type" content="text/html;charset=utf-8">
<TITLE>301 Moved</TITLE></HEAD><BODY>
<H1>301 Moved</H1>
The document has moved
<A HREF="http://www.google.com/">here</A>.
</BODY></HTML>
```
The option *--rm* took care of the container and removed it after it closed after the curl, and also of course exited me from the container, as it did not exist anymore.

## Exercise 1.3 - Alternative way
As the docker image I are using (Ubuntu 16.04) does not have curl installed beforehand, I have to install it. To get the installation running, I have to update apt-get repositories first, then install curl.
```
$ docker run -d --rm -it --name curler ubuntu:16.04 sh -c 'read website; sleep 3; curl http://$website' && docker exec curler apt-get update -y && docker exec curler apt-get install -y curl
```

To use the commandline of the *curler* container, I use the command
```
$ docker attach curler
```
Here I typed *google.com* to get
```
$ google.com
<HTML><HEAD><meta http-equiv="content-type" content="text/html;charset=utf-8">
<TITLE>301 Moved</TITLE></HEAD><BODY>
<H1>301 Moved</H1>
The document has moved
<A HREF="http://www.google.com/">here</A>.
</BODY></HTML>
```
The option *--rm* took care of the container and removed it after it closed after the curl, and also of course exited me from the container, as it did not exist anymore.

## Exercise 1.4
Creating my first own Dockerfile:
```
FROM node:latest
EXPOSE 5000

WORKDIR /homie
COPY / .
RUN npm install
```
To build the image:
```
$ docker build -t node-example .
```
And theeeeen to run the app:
```
$ docker run -d --name node-app -p 5000:5000 node-example sh -c 'npm start'
```

## Exercise 1.4 - Alternative
Creating my first own Dockerfile:
```
FROM node:latest
EXPOSE 5000

WORKDIR /homie
COPY / .
RUN npm install

CMD npm start
```
To build the image:
```
$ docker build -t node-example .
```
And theeeeen to run the app:
```
$ docker run -d --name node-app -p 5000:5000 node-example
```
Notice the difference in the *Dockerfile* and the *docker run* command. The start command is now in the *Dockerfile*.

## Exercise 1.5
Creating my second own Dockerfile:
```
FROM node:latest
EXPOSE 8000

WORKDIR /homie
COPY / .
RUN npm install
RUN touch logs.txt

CMD nmp start
```
I use the image of the latest node. I expose the port 8000 and create a home folder. As the *Dockerfile* is in the project root, I copy the project root as is. Then I install the npm packages, create *logs.txt* and run the start command.  
To build the image:
```
$ docker build -t node-back-example .
```
And then to run the app:
```
$ docker run -d --name node-back-app -v $(pwd)/logs.txt:/homie/logs.txt -p 8000:8000 node-back-example
```
To make sure the mount worked, I had to create the logs.txt file locally first. Otherwise, docker mounting would create a *logs.txt* folder, and the mount would not work.