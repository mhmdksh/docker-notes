# Notes to Work with Docker
## Usages
### Run a docker image
	docker run [IMAGE_NAME]
ex:

	docker run hello_world

### Run docker image with bash

	docker run -it ubuntu bash

### List all containers

	docker ps -a

### List last running container

	docker ps -l

ex:
	
	docker ps -l
	
		CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                      PORTS               NAMES
		557b2005f6c6        ubuntu              "bash"              37 seconds ago      Exited (0) 11 seconds ago                       happy_chatterjee

### Commit a docker container, to produce a separate image (save it as a new image)

	docker commit [CONTAINER_NAME] [NAME_OF_IMAGE_TO_BE_SAVED]

ex:

	docker commit happy_chatterjee my_image

### Commit a docker container, to produce a separate image with a tag

	docker commit [CONTAINER_NAME] [NAME_OF_IMAGE_TO_BE_SAVED]:[TAG]

ex:

	docker commit happy_chatterjee my_image:v2.1

### Run docker image with bash and keep it running

	docker run -d -it ubuntu bash

### List running docker containers

	docker ps

		CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
		00169465cdaf        ubuntu              "bash"              6 minutes ago       Up 6 minutes                            loving_hugle

### Exit a docker container, while keeping it running

	CTRL + p
	CTRL + q

### Connect to a running docker container

	docker attach [CONTAINER_NAME]

ex:
	
	docker attach loving_hugle

### Execute a process in a running container

	docker exec -ti [CONTAINER_NAME] [POCESS_NAME]

ex:

	docker exec -ti loving_hugle bash

### Examine the logs of a container

	docker logs [CONTAINER_NAME]

ex:
	
	docker logs loving_hugle

### Stop a container

	docker stop [CONTAINER_NAME]

### Remove a container

	docker rm [CONTAINER_NAME]

### Remove an Image

	docker rmi [IMAGE_NAME]

### list all container IDs

	docker ps -aq

### Stop all containers

	docker stop $(docker ps -aq)

### Remove all containers

	docker rm $(docker ps -aq)

## Linking

### Linking: To make port 80 inside a container accessible from the internet on port 8080 run ##

	docker run -p 8080:80

### Linking: Run a docker container, with ports 1234 from inside that translates to port 1234 from outside the container ##

	docker run --rm -ti -p 1234:1234 --name echo-server ubuntu:14.04

### Linking: Run a docker container, with ports 1234 from inside that translates to any port from outside the container ##

	docker run --rm -ti -p 1234 --name echo-server ubuntu:14.04

### Linking: Specify that the ports is UDP (it is TCP By default) ##

	docker run --rm -ti -p 1234/UDP --name echo-server ubuntu:14.04

### Linking: Link a docker container to another docker running as a server ##

	docker run --rm -ti --link [SERVER_Container_Name] --name client ubuntu:14.04

ex:

	docker run --rm -ti --link server --name client ubuntu:14.04

## Networking
### Networking: Create a docker network

	docker network create [Network_Name]

ex:

	docker network create private

### Networking: Link a docker container to a created network

	docker run --rm -ti --net=[NETWORK_NAME] --name [CONTAINER_NAME] ubuntu:14.04 bash

ex:

	docker run --rm -ti --net=private --name server ubuntu:14.04 bash

### Networking: Link a docker container client to a contaner server and connect it to the same network used by the server

	docker run --rm -ti --link server --net=private --name client ubuntu:14.04 bash

### Networking: Remove network protection between the docker containers and the host machine

	docker run --rm -ti --net=host options image-name command
ex:
	
	docker run --rm -ti --net=host ubuntu bash

## Volumes
### Volumes: Share a folder from host machine to a folder inside the docker container ##

	docker run --rm -ti -v [HOST_MACHINE_DIR]:[CONTAINER_SHARED_DIR] ubuntu:14.04 bash
ex:
	
	docker run -ti -v /root/docker_shared/:/root/docker_shared ubuntu:14.04 bash

### Volumes: Create a volume that is not shared with the host ##

	docker run -ti -v [DIR_of_Non_Shared_Volume] ubuntu:14.04 bash
ex:

	docker run -ti -v /root/docker_shared/ ubuntu:14.04 bash

### Volumes: Run a docker container that is connected to an already existing shared volume ##

	docker run -ti --volumes-from [CONTAINER_NAME] ubuntu:14.04 bash
ex:

	docker run -ti --volumes-from priceless_almeida ubuntu:14.04 bash

## Docker_Files (Used to build Docker Images)

SYNTAX:

Start from a specific docker image (Should always start with it in the beggining of the Dockerfile)
Also can start from more than one image, (Which will result in creating more than one image)

	FROM [STARTING_IMAGE_NAME]

### Make the auther of the dockerfile for documentation purpose

	MAINTAINER [FIRSTNAME] [LASTNAME] <[email.address@domain.com]>

### Running commands in the containers created and save them within teh dockerfile build

	RUN [SHELL_COMMAND] [/DIRECTORY/IN/CONTAINER]

ex:

	RUN unzip install.zip /opt/install

### Add files, extract files, download and send files, from host machines into images

	ADD [HOST_FILE] [IMAGE_FILE]

ex:
	
	ADD run.sh /run.sh

	ADD project.tar.gz /install/
	
	ADD https://project.example.com/downloads/big-file-from-the-internet.rpm /project/
	
### Set environment variable, during the run of the Dockerfile, and after the image is created

	ENV [VARIABLE]=[SOME_VALUE]

ex:

	ENV DB_HOST=db.production.example.com
	
	ENV DB_PORT=3306
	
NOTE:

Unlike the RUN Command, the ENTRYPOINT & CMD Commands are commands that makes the built images, run a command after it finished creation and started

### ENTRYPOINT & CMD forms of execution

Shell Form:

	CMD nano notes.txt
	
Exec Form:

	CMD ["/bin/nano", "/notes.txt"]
	
### Expose network ports on the created container

	EXPOSE [PORT_NUMBER]

ex:

	Expose 8080
	
### Define Volumes [Directories] to be shared with container image

	VOLUME ["/host/path" "/container/path"]
	VOLUME ["shared-data"]

NOTE:
Avoid defining shared folders in docker files since, this will mean all the datathat resides is only going to be usefull if you use it on the same host manchine
	  
### Set the working directory of the container that is starts in

	WORKDIR [DIR]

ex:

	WORKDIR /install/

### Set which user the container will run as

	USER [USERNAME]

ex:

	USER alex
	USER 1000
	
### Process of building a docker file to create a usable image

1. Create the DockerFile in the directory you are working with.
2. Make sure the docker file is called "Dockerfile".
3. Run the docker build command as shown below.

Docker Build Command:

	docker build -t [NAME_OF_IMAGE_TO_BE_SAVED] .
 ex:

	docker build -t new-ubuntu .
	
### Save Docker Image to a comppressed file

	docker save -o [TAR_GZ_COMPRESSED_FILE] [IMAGE1] [IMAGE2] ....

ex:
	
	docker save -o ubuntu_images.tar.gz ubuntu:12.04 ubuntu:14.04 ubuntu:16.04

### Load saved docker images from compressed file

	docker load -i [TAR_GZ_COMPRESSED_FILE]

ex:

	docker load -i ubuntu_images.tar.gz
