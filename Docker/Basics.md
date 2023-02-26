# Docker Architecture
![](Images/Pasted%20image%2020230126033407.png)

> **containerd** manages containers
> **containerd shim** manages containers when containerd daemon is down or restarting
> **runC** is container runtime used for spawning and runnign containers
> **libcontainer** is the new executing driver in docker engine

> Docker CLI, rest api and coker daemon are the main components of DOCKER ENGINE.
> **Images, Volumes , containers and networks are the objects that are managed by docker daemon containerd**


<br/>


# Containers vs Virtualisation
 ![](Images/Pasted%20image%2020230126033431.png)

<br/>

---


# <span style="font-family:default; font-size:default; color:red">Location of files related to docker and its containers is <u>/var/lib/docker</u></span>

---


<br/>

# Docker image vs Container
![](Images/Pasted%20image%2020230126033454.png)

<br/>

> <span style="font-family:default; font-size:default; color:red">Dockerfile</span> contains instructions for the image. 
> The docker image is created from a Dockerfile using `docker build .` command.
> We can also use `docker build -f customName .` for building from a dockerfile with a custom name other than dockerfile
> 

<br/>


---

<br/>

> 
> `docker run imageName` command is used to ***Start a container***
> - docker run looks for a local/already pulled image to run. Otherwise pulls an image from docker hub and then runs it.
> 
> - We can also use the `docker pull imageName` to just download the image and store it on our host without running it.


> `docker run --cpus=.5 ubuntu` or `docker run --memory=100m ubuntu`
> can be used to restrict cpu/mem utilization of the host by the container.
> By default it can use 100% of host resources if required.
---



<br/>

`docker ps` command lists ***all the running containers***
![](Images/Pasted%20image%2020230126033518.png)

- `docker ps -a` lists all the *running and stopped* containers.

<span style="font-family:default; font-size:default; color:red">docker ps shows basic information about container. To get detailed information, use inspect command</span>`docker inspect containerName` <span style="font-family:default; font-size:default; color:red">which will show the details in json format</span>

> We can use `inspect` to get details like IP address of container

<br/>

- `docker stop containerName` to stop the running container


<br/>

---

<br/>

`docker rm comtainerName` to *remove the container* even from the stopped containers list.
- Can verify with `docker ps -a` 

<br/>

---

<br/>

`docker images` *lists the images* present on the host locally.
![](Images/Pasted%20image%2020230126033543.png)

<br/>

---

<br/>

`docker rmi imageName` to remove a locally pulled image.
Can verify with docker images command.
##### MUST STOP AND DELETE THE CONTAINERS MADE FROM THE IMAGE TO USE THE `rmi` COMMAND


<br/>

---


<br/>

# To stop all the containers at once.. even the running and non running ones

To stop all the containers at once, run the command: `docker stop $(docker ps -aq)`  
To remove all the stopped containers at once, run the command: `docker rm $(docker ps -aq)`

<br/>

---

<br/>

# To remove all **Stopped** containers
 `docker container *prune*`

<br/>

---

<br/>

# To run docker image in daemon mode.. use `-d` flag

# To run an image with a specific name... use `docker run --name Name `
> Must not use same --name again if used already for running container. 

<br/>

 ## To rename a container 
	 `docker container rename *oldName* *newName*`
---

<br/>


# Running a container in **interactive** mode and connecting to the **terminal**
`docker run -it imageName`
> here -i is for interactive and -t is for connection to terminal.
> Normally if we run ubuntu container, its entrypoint command BASH runs which tries to find a terminal. by default docker doesnt provide a terminal.
> So we use `-t` for attaching a terminal.

> <span style="font-family:default; font-size:default; color:red">Interactive</span> mode is so that we can give inputs to the stdin of the container. 

> Normally container runs, does the process and ends. But Sometimes we need a continusly running container like a web server container. while its running, by default it will block the console. Hence we can use the `-d` flag for detached mode.

> We can also use the `-itd` flag to run in detached and interactive mode

<br/>

---


<br/>

# Port Mapping
`docker run -p 80:5000 imageName`
> Here 80 is the available port in the docker host which is mapped to the port 5000 of the container.

![](Images/Pasted%20image%2020230126033602.png)


<br/>

---


<br/>


## To get logs of the container which we ran.. we can use `docker logs containerName`

To get realtime logs, use `-f ` flag

<br/>

---


<br/>

# Containerizing an application with Dockerfile using docker build
- First try to run the application manually and note down steps
- Create a Dockerfile and put the steps
<span style="font-family:default; font-size:default; color:red">Must define a base os image/ docker image and put it in `FROM` </span>
- Then run `docker build . -t anyTagName` 

> While building a docker image using `docker build` we can use the `-t` flag to give a name and tag in the format **name:tag**.
> While using `docker run` we can also add a name using `--name`tag

> Once we use docker build to build an image, it is read only 

<br/>

---

<br/>

## To set env vars while running a container, use `-e` flag 
`docker run -e KEY=value`


<br/>

---

<br/>

# ENTRYPOINT vs CMD in dockerfile

> In case of <u>ENTRYPOINT</u>, 
> whatever we pass with `docker run` is **appended** to the ENTRYPOINT

> Can directly put commands like `ENTRYPOINT sleep 10`
> or can use json like `ENTRYPOINT {"sleep", "10"}`


> In case of <u>CMD</u>,
> Whatever we pass with `docker run` **replaces** the CMD 


---

<span style="font-family:default; font-size:30; color:red"><u>Using ENTRYPOINT and CMD together</u></span>
> Example:
> We need to run command `sleep 10`
> We can use the ENTRYPOINT **sleep** and then use `docker run ubuntux 10`
> That will append <u>10</u> to <u>sleep</u> .
> But if we just run `docker run ubuntux` then the entrypoint sleep will give error.

> For fixing that, we can set a default behaviour using CMD
> If we specify CMD as 10, then running `docker run ubuntux` will use "sleep 10" by combining entrypoint and cmd.
> Also now if we specify 20 manually like `docker run ubuntux 20`, then CMD will get overwritten and "sleep 20" will be passed.
> <span style="font-family:default; font-size:default;font-weight:bold; color:red">Must pass commands in json if wanna use CMD and ENTRYPOINT together</span>

> We can also overwrite entrypoint using `--entrypoint` flag while using run.


<br/>

---

<br/>

# Docker compose
To run various images together, we can use a docker-compose.yml file
and put the image with specifications there
``` 
services:
	redis:
	 image: redis:alpine
	clickcounter:
	 image: kodekloud/click-counter
	 ports: - 8085:5000
version: '3.0'

```

`docker-compose up -d` to run the containers.

Can use `-f ` flag for custom filename.

Can use `docker-compose down` to stop the containers and networks created.


<br/>

---

<br/>

# Volumes

## To map a volume on the docker host to a volume inside a container
<span style="font-family:default; font-size:default; color:red"> So that data is persisted after container is removed and the container can use the persisted data when new container is up</span>
`docker run -v /pathOnHost:/pathInsideContainer imageName`

<br/>

<u>Alternate Syntax (preffered)</u>  
``` 
docker run\ 
--mount type=bind,source=/pathOnHost,target=/pathInsideContainer imageName
```


## <u>Docker volume</u> mapping vs <u>Bind mounting</u>
> In case of Docker volume mapping, we use map the folders inside /var/lib/docker/volumes to the containers path.
> Example: `docker run -v data_vol:/pathInContainer`
> This will map /var/lib/docker/volumes/data_vol to /pathInContainer
> <br/>
>
> In case of Bind mounting, We mount any location on the docker host to the pathInContainer




### For cloud storage
![](Images/Pasted%20image%2020230213024713.png)
<br/>

---

<br/>


# Networking
`docker network ls` to see the available networks (usually bridge, host and none)
`docker network inspect networkName` to get the subnet and other properties 
<br/>
### To run a container in a specific network
`docker run --name alpine-2 --network=none alpine`
<br/>
### To create a network using a specified driver like bridge or host
`docker network create --driver bridge --subnet 182.18.0.1/24 --gateway 182.18.0.1 wp-mysql-network`


<br/>

---

<br/>

# To escape from a container shell without exiting the container    `cntrl + p + q`

<br/>

---


<br/>
<br/>
<br/>


# New Format
![](Images/Pasted%20image%2020230126033634.png)

## New Format Commands examples

 ### Container object
> `docker container create *containerName*`
> alternative of docker pull

> `docker container stats`
> displays cpu, memory, etc

> `docker container logs *containerName*`
> Can use `-f` flag for streaming logs live




---

 ### To execute a command on a container **Running** in detached mode

`docker container exec *containerId* *commandToBeExecuted*`

---
 ### Docker system event details of a container
`docker system events --since 60m`

---
`docker container pause *containerName*`
or unpause comamnd are used to pause container with state saved

---

 ### To send a kill signal to container
`docker container kill --signal=9 *contName*`






# To run docker image with all privilages or restricted privilages

use `docker run --cap-drop KILL ubuntu `
OR
`docker run --privilages ubuntu`