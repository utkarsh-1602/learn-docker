# docker-commands

→ ************************************build the image :************************************ docker build -t <image-name>

→ ****************run the image container :**************** docker run -dp 127.0.0.1:3000:3000 <image-name> 

**************************→ run the image container without port :************************** docker run -i -t <image-name>

→ ********list of your containers :******** docker ps 

After updating the application, you should build a new image and start a new container 

**********************************→ delete container :********************************** docker rm -f <the-container-id> 

To share docker image publically, you have to use docker registry. 

**************************************************************************************************→ signup on dockerhub : (remember your dockerId - utkarshhadgekar)**************************************************************************************************

****************→ create a new repo on dockerhub (eg. docker-repo)****************

→ **************************************************************************************************for the first time login of docker hub from terminal, you need to generate gpg key, 
follow this tutorial (************************************************************************************************** [https://docs.docker.com/desktop/get-started/#:~:text=Sign in to Docker Desktop](https://docs.docker.com/desktop/get-started/#:~:text=Sign%20in%20to%20Docker%20Desktop))

→ **************************************************************************************************signin dockerhub :************************************************************************************************** docker login -u <dockerId>

**************************************************************************************************→ You need to tag your local Docker image with the Docker Hub repository name :************************************************************************************************** 
docker tag <local-image-name> dockerId/docker-repo

********************→ push repo to dockerhub :******************** docker push dockerId/docker-repo

But now all this time, you are running your image locally ( localhost://), but to run your image on a new instance, you have to use **********************************Play with docker.********************************** 

********************→ go to play with docker → login → add new instance******************** 

→ **************************run this on terminal :************************** docker run -dp 0.0.0.0:3000:3000 dockerid/docker-repo

→ **************select/open port************** 

Congratulations ! your docker image is running on a new instance. 

************************→ watch docker logs of container :************************ docker logs -f <container-id>

Each time you delete a container, and starts the new one, the previous saved data get lost. While containers can create, update, and delete files, those changes are lost when you remove the container and Docker isolates all changes to that container. With volumes, you can change all of this.

********************→ create volume :******************** docker volume create <volume-name>

**→ Starting the container, but add the `--mount` option to specify a volume mount. Give the volume a name, and mount it to (file location) in the container, which captures all files created at the path :** 
`docker run -dp 127.0.0.1:3000:3000 --mount type=volume,src=todo-db,target=/etc/todos <image-name>`

********************************************************→ To see where docker is storing data when you use Volume :******************************************************** docker volume inspect 

Using Bind mount, you are able to update your container, even if you make changes to your files and saved it. you can use the package nodemon. 

→ ************************************run your app in development container :************************************ 

```docker
docker run -dp 127.0.0.1:3000:3000 \
    -w /app --mount type=bind,src="$(pwd)",target=/app \
    node:18-alpine \
    sh -c "yarn install && yarn run dev"
```

******************************→ to know if your container is updating or not, you can watch the logs :****************************** docker logs <container-id>

Container networking : If you place the two containers on the same network, they can talk to each other. 

****************************→ create the network :**************************** docker network create todo-app 

****************→ starting mySQL container and attaching it to network :**************** 

```docker
docker run -d \
    --network todo-app --network-alias mysql \
    -v todo-mysql-data:/var/lib/mysql \
    -e MYSQL_ROOT_PASSWORD=secret \
    -e MYSQL_DATABASE=todos \
    mysql:8.0
```

**→ this command is used to start an interactive session within a running MySQL Docker container, where you'll be prompted for the MySQL root user's password after execution :** docker exec -it <mysql-container-id> mysql -u root -p 

****************→ show list of Docker networks on your system :**************** docker network ls

******************→ delete all docker containers on your system :****************** docker rm -f $(docker ps -aq)

**********************→ delete a docker network :********************** docker network rm <network-name-or-id>

**************************************************→ delete a docker volume :************************************************** docker volume rm <volume-name-or-id>

dig: is a command-line utility for querying DNS (Domain Name System) servers. It is used to look up information about domain names, such as IP addresses, name servers, and other DNS-related data.

→ **********query the DNS server to retrieve information :********** dig mysql 

Multi-container application : you can store your data into an external database running in a separate container. 

Docker compose : a tool that helps you define and share multi-container applications.Docker Compose simplifies the management of multi-container applications, allowing you to define the entire application stack in a single configuration file, making it easier to develop, deploy, and scale complex applications.

→ ************************************************************************After creating and updating the .yaml file, you can run the application stack using :************************************************************************
docker compose up -d 

→ **********to delete the multi-container application :********** docker compose down 

→ ****************to see the image layers and their history :**************** docker image history <image-name>

Layer caching : every time you create the container or image, you have to install dependencies. its not a best practice to install same dependencies every time you build. so instead of that, define the dependencies in your docker file. 

→ **********************define dependencies in your Dockerfile :**********************  COPY package.json yarn.lock ./

→ ********************add all files and dependencies of docker :******************** docker init