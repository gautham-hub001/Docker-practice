# Docker-practice from linkedin learning course

Previous solution: virtual machines
Disadvantage:
They are heavy weight
Take a lot of disk space
Apps cannot interact with one another
VMs need to be booted up first before starting the app

apps are packaged into images
images run on containers. containers are created from images

images are created from light weight configuration files

Advantages of docker:
build apps quicly, safely and cost efficiently

1. virtual machines:
   virtualize hardware. Each VM is on top of the hypervisor which is on top of host hardware. Apps run in individual VMs which have seperate guest OS for each app.
2. containers:
   virtualize OS kernels. Docker lies on top of host OS which is on top of host hardware and apps run on top of docker

You can share images on Docker Hub

Containers are composed of Linux control groups and Linux namespaces
control groups restrict resources that the containers can use like memory, processor time,
namespaces allows admins to restrict what processes can be seen on a system

So, Docker is basically designed to work on Linux only. It uses VM to work on Mac and Windows.
So, we need Docker Desktop is used for this. It runs on Apple's own hypervisor(Virtual Kit) and Hyper-V on windows.

## Docker intallation for mac

1. download and Install docker from official page
2. verifying if docker is installed properly:
   i. open terminal
   ii. docker run --rm hello-world
   This will retrieve a docker image called hello-world and create a container from it
   You'll get a hello message
   because of --rm option, docker will automatically delete the container after it is done running the app.

## Docker intallation for windows

1. download and Install docker from official page
2. After installation it will ask if you want to use wsl2 or Hyper-V for running the docker engine. Choose wsl2
3. verifying if docker is installed properly:
   i. open powershell
   ii. docker run --rm hello-world

## Docker intallation for Linux (Ubuntu distribution)

1. open terminal
2. check if curl is installed: sudo apt install curl
3. Download docker's installation script and save it to /tmp/get-docker.sh
   i. curl -o /tmp/get-docker.sh https://get.docker.com
   ii. sh /tmp/get-docker.sh
4. verifying if docker is installed properly:
   i. docker run --rm hello-world
5. Since we don't to use sudo everytime we want to run docker:
   sudo usermod -aG docker $USER
   -a is for adding new user
   G is for specifying group name (docker)
   $USER is username
6. restart terminal
7. run:
   groups -> to see if username is added to the groups

# Docker commands:

1. top level commands: ps, run, pull network
2. docker network --help
   This gives subcommands list:
   create
   connect
   disconnect
   ls
   rm
3. create docker container (hello-world linux version image :linux is called image tag): docker container create hello-world:linux

After the command gets executed its container id will be displayed too. And the container can be seen in the docker desktop app.

4. listing running containers: **docker ps**
5. listing all containers: docker ps --all or docker ps -a
   In the status column, if we get Exited(0): it's a good thing. It means the entry point has executed successfully.

   Listing only the container ids: docker ps -aq

6. retrieving docker images from image registry if they're not available locally (by default the registry is docker hub) and running them: docker run --rm hello-world
7. run a container: docker container start c6d3562ab828
   Note: You can run docker container start against a container multiple times.
8. Now check if the container ran successfully using the docker ps --all command
9. To check logs of what happened when the app ran (using first chars of container id): docker logs c6d
10. To get logs directly while running the container, use --attach tag: docker container start --attach c6d

Note: You can also see all the logs in the docker desktop app.

11. Shortcut to create, start, and see logs of containers (in single command): docker run hello-world

**To name a container:**
docker run --name my-container hello-world
or
docker run --name=my-container hello-world
docker container run = docker container create + docker container start + (--attach / docker container logs)

12. Building image from our own docker file:
    docker file syntax:
    FROM ubuntu(baseimage)

    LABEL maintainer="Gautham"(extrainfo)

    USER (which user to use for the commands under this. By default it's root)

    COPY ./entrypoint.bash/ (also called as **context**)(copies files from the directory, to the docker image. It is generally the working directory)

    RUN apt -y update (for customizing the image)

    ENTRYPOINT ["/entrypoint.bash"] (what commands should be run by the container)

    ## Creating docker image from docker file: docker build

    docker build -t image-tag-name context --file dockerfilename
    By default, it will check for Dockerfile. If you have a different name, you need to specify it using --file/ -f tag.

    example: docker build -t my-first-image .

    Note: docker creates images for each command in the dockerfile. These are called intermediate images. At the end of the Dockerfile, it squashes all these images into a single images.

    Now create the container
    docker run my-first-image

13. running containers which run for long time/ forever (like servers) without attaching to the logs: docker run -d my-server
14. Stop a running container: docker stop d2d112
15. Removing a stopped container: docker rm d2d112
    Note: rm only removes containers which are already stopped.
    Removing any container (stopping and removing it if it is not already stopped): docker rm -f d2d112
16. Stopping and removing all the existing containers : docker ps -aq | xargs docker rm
    | (pipe) is for taking output on the left and feeding it to the right
    xargs is for feeding the output one-by-one
17. Removing a stopped image: docker rmi my-first-image
    Note: Sometimes docker does not allow to remove images of containers that are running. So, you need to stop the containers using that image before removing the image.
18. List of images: docker images

Using --entrypoint you can write some shell commands
docker run --rm --entrypoint sh ubuntu -c "echo 'Hello there' > /tmp/file && cat /tmp/file"

**Port Binding:**
Allows docker to bind port on Host to a port on the container.
-p switch is used. -p host-physical-port:docker-port (port that is mentioned in the code) or -p outside:inside
docker run -d --name my-container-name -p 5001:5000 my-image-name

**Volume binding:**
Everything you create within a container, stays within the container. Once the container stops, the data gets deleted with it. So, if your app wants to save files after it stops running, you can use volume mounting. It maps a folder in the host system to a folder in the container.
-v or --volume
-v outside:inside
docker run --rm --entrypoint sh -v /tmp/container:/tmp ubuntu -c "echo 'Hello there' > /tmp/file && cat /tmp/file"

If you cat /tmp/container/file you'll get Hello world

Note: If your host file does not exist. It will assume it is a directory and it will give you an error saying "Is a directory"
docker run --rm --entrypoint sh -v /tmp/this_file_does_not_exist:/tmp ubuntu -c "echo 'Hello there' > /tmp/file && cat /tmp/file"

**Docker Hub** : It is a registry used for pushing and pulling images.
Create an account
Now, login in the docker cli

> > docker login

To push image into docker hub, use tag command
docker tag image-name docker-hub-username/image-name-you-want-to-save-as:version-number
docker push docker-hub-username/image-name-you-want-to-save-as:version-number

Note: version-number is optional. By default, it is going to be latest
docker tag hello-world gauthamhub001/hello-world
docker push gauthamhub001/hello-world

**Practice problem**
Use nginx to Serve website using nginx on host port 8080 and app port 80. Renaming container as website.
$PWD/website should point to /usr/share/nginx/html
website files that need to be deployed are given already in website folder
The container needs to be deleted after the container is stopped.

Solution:
docker run --name website --rm -p 8080:80 -v $PWD/website:/usr/share/nginx/html nginx

19. Reclaim space not being used: docker system prune

Note: Alpine is Linux's extremely small and bare bonces kind of distribution

**Note: To run a container indefinitely, use --entrypoint=sleep and use infinity argument**
eg. docker run --name=alpine --entrypoint=sleep -d alpine infinity

docker: Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemon running?.
solution:
For windows and mac:
Open docker desktop app, if it is not open. If it is open, restart the app

For Ubuntu,
service docker restart
or
systemctl docker restart

## If your container is too slow, do the following to know its performance (docker stats, docker docker exec container-name sh, docker top):

20. Check docker stats: docker stats container-name
    eg. docker stats alpine

If you want interactive mode: -i tag

21. **If you want to enter a container's terminal** (create a virtual terminal) (sudo tty): -t tag

1)  Suppose, there is alpine running in a container. > docker stats alpine gives stats
2)  open another terminal
3)  docker exec -i -t container-name shell-you-want-to-use
    **eg. docker exec -i -t alpine sh**
4)  To increase CPU consumption, you can use yes program
    > yes
5)  To know what's happening inside the container without having to exec into it: **docker top container-name**
    eg. If you put the same container to sleep multiple times
    docker exec -d alpine sleep infinity
    docker exec -d alpine sleep infinity
    docker exec -d alpine sleep infinity
    docker top alpine
6)  To get stats in JSON format: **docker inspect container-name**
    eg. docker inspect alpine
    Adv: Use can filter easily and you get a lot of data too
    To filter for less data: **docker inspect container-name | less**
    You get a viewer (Pagan 80) when you run the less command.
    type q to exit
    type /Res and hit enter to search for Res in the viewer
