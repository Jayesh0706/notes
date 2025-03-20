### **Beginner Commands:**

-  **`docker --version`**
    - Check the Docker version installed on your system.
    - Example: `docker --version`

-  **`docker pull <image>`**
    - Download a Docker image from Docker Hub.
    - Example: `docker pull ubuntu`

-  **`docker build -t <tag> <path>`**
    - Build a Docker image from a Dockerfile located at a specified path.
    - Example: `docker build -t myimage .`

-  **`docker images`**
    - List all locally stored Docker images.
    - Example: `docker images`

	
- `docker rmi <my_image/id>`
	  - Remove an image 
	  - Example: ``docker rmi ubuntu``

-  **`docker ps`**
    - List running containers.
    - Example: `docker ps`

- ``docker ps -a``
	 -  **List all containers** (including stopped ones).
	 - Example: ``docker ps -a

-  **`docker run <image>`**
	- Run a container from a specified image.
    - Example: `docker run ubuntu`

- ``docker run -d <image_nam/id>``
	 - **Run a container in detached mode** (background). #usethis beacause without -d powershell getting error 
	 - Example: ``docker run -d ubuntu

- `docker run --name <container_name> <image_name>`
	 - Run container with specific name 
	 - Example: `docker run --name mycontainer nginx`
	 - It will create container named mycontainer with base image of nginx


- `docker run -it --name mycont nginx bash`
     - Runs container named mycont with base image nginx and also gives bash shell(command prompt inside container) 


- `docker run -p <host_port>:<container_port> <image_name>`
     - Example:`docker run -p 8080:80 nginx`
     - This runs an Nginx container and maps port `8080` on the host to port `80` inside the container.
--- 

#bestway -best way to launch container of nginx or any other
- `docker run -d -p 8080:80 --name myweb nginx`
     - run an **Nginx** container in **detached mode** and expose port `80` from the container to port `8080` on the host.

- `docker run -it -p 8080:80 --name myweb nginx bash
     - This will **start the Nginx container** but override the default entry point (which is Nginx) with a `bash` shell instead, allowing you to interact with the container's file system or run commands inside it.
`
--- 

-  **`docker exec -it <container_id> <command>`**
    - Run commands inside a running container.
    - Example: `docker exec -it mycontainer bash`
    - This command open up interactive bash terminal inside container

-  **`docker stop <container_id>`**
    - Stop a running container.
    - Example: `docker stop mycontainer`

- `docker stop <container1> <container2> <container3>
     - Stop multiple containers at once:
     - Example:`docker stop cont1 cont2 1233aec2` (with name or id)

- `docker stop $(docker ps -q)`
     - `docker ps -q` lists ==all the running container== IDs, and `docker stop` stops them 

- `docker rm $(docker ps -a -q)`
     - Remove All Containers: Once the containers are stopped, you can remove them with this command
 

-  **`docker rm <container_id>`**
	- Remove a stopped container.
    - Example: `docker rm mycontainer`

- `docker rmi $(docker images -q)`
     - To remove all Docker images from your system:

- `docker rmi -f <image_id_or_name>`
     - To **remove an image** that is currently being used by a container, you would typically encounter an error because ==Docker prevents you from removing an image that’s being used==.

- `docker rm -f <container_id_or_name>`
     - **remove a running container**, you can't do so directly without stopping it first so we use -f/--force
 
- `docker run -d --name mysqlcont -v <ec2_backup_storage_path>:<container_backup_file_path -e MYSQL_ROOT_PASSWORD=pass1234 mysql>`
 
     - ==-v== --> used to attach volume to container (we give ec2 folder that stores backup : with container's file that should have backup(eg. database, imp files etc.)  )


https://stackoverflow.com/questions/34357252/docker-data-volume-vs-mounted-host-directory