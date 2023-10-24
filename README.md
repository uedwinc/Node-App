## Profile App with Database

Deploying a nodejs application with mongodb database.

This app shows a simple user profile app set-up using 
- index.html with pure js and css styles
- nodejs backend with express module
- mongodb for data storage
- Then, there's mongo express to serve as UI for mongodb

All components are docker-based

---

### Tools and Packages

![linux][linux]  ![aws][aws]  ![git][git]  ![docker][docker]  ![nodejs][nodejs]  ![mongo][mongodb]  ![sonatype][sonatype]

[linux]: <https://img.shields.io/badge/linux-FCC624?style=for-the-badge&labelColor=black&logo=linux&logoColor=FCC624>
[aws]: <https://img.shields.io/badge/amazonaws-232F3E?style=for-the-badge&labelColor=black&logo=amazonaws&logoColor=232F3E>
[git]: <https://img.shields.io/badge/git-F05032?style=for-the-badge&labelColor=black&logo=git&logoColor=F05032>
[docker]: <https://img.shields.io/badge/docker-2496ED?style=for-the-badge&labelColor=black&logo=docker&logoColor=2496ED>
[nodejs]: <https://img.shields.io/badge/nodejs-339933?style=for-the-badge&labelColor=black&logo=nodedotjs&logoColor=339933>
[mongodb]: <https://img.shields.io/badge/mongodb-47A248?style=for-the-badge&labelColor=black&logo=mongodb&logoColor=47A248>
[sonatype]: <https://img.shields.io/badge/sonatype-3-1B1C30?style=for-the-badge&logo=sonatype>

---

## 1. Running the Containers Locally

### Running the Database Containers

1. First, we clone the git repository to be used

2. Create a docker network to link resources between different docker containers

    ```docker network create mongo-network``` mongo-network is the name of the network.

    ```docker network ls``` to see the list of networks

In case you want to remove a network, use: ```docker network rm name-of-network```

3. Next, Run the containers using the official mongo and mongo-express images following directives from dockerhub

**For mongo**

```bash
docker run -d \
-p 27017:27017 \
-e MONGO_INITDB_ROOT_USERNAME=admin \
-e MONGO_INITDB_ROOT_PASSWORD=password \
--name mongodb \
--net mongo-network \
mongo
```

**For mongo-express**

```bash
docker run -d \
-p 8081:8081 \
-e ME_CONFIG_MONGODB_ADMINUSERNAME=admin \
-e ME_CONFIG_MONGODB_ADMINPASSWORD=password \
-e ME_CONFIG_MONGODB_SERVER=mongodb \
--name mongo-express \
--net mongo-network \
mongo-express
```

![mongo ps](https://github.com/uedwinc/NodeApp-with-Docker/blob/main/images/mongo%20ps.png)

- Now, access the mongo ui on the browser using ip address and port

![mongo ui](https://github.com/uedwinc/NodeApp-with-Docker/blob/main/images/mongo%20ui.png)

- Create a database called 'user-account', and a table/collection called 'users'

![user-account](https://github.com/uedwinc/NodeApp-with-Docker/blob/main/images/user-account.png)

![users](https://github.com/uedwinc/NodeApp-with-Docker/blob/main/images/users.png)


### Starting the NodeJS Application Locally

1. First, navigate to the app directory.

2. Go to **index.html** and map your own ip address to the fetch url

3. Since we want to run the application locally, go into **server.js** and make sure you configure "mongoClient.connect" to use "mongourlLocal"

4. Then, we need to run ```npm install && npm audit fix```

- Use ```apt install npm``` if not already installed

5. Now, run ```node server.js``` to start the application

![node listen](https://github.com/uedwinc/NodeApp-with-Docker/blob/main/images/node%20lsiten.png)

- Make sure to open port 3000 up in the security group

6. Access the webpage on the ip address and the listening port (3000)
    
![page](https://github.com/uedwinc/NodeApp-with-Docker/blob/main/images/page.png)

7. Let's edit the profile page

![profile edit](https://github.com/uedwinc/NodeApp-with-Docker/blob/main/images/profile%20edit.png)

- We can see this reflect on the database

![info mongo](https://github.com/uedwinc/NodeApp-with-Docker/blob/main/images/info%20mongo.png)

---

## 2. Using Docker Compose

1. Go into **server.js** in the app directory and configure "mongoClient.connect" to use "mongoUrlDockerCompose". If necessary, edit the **index.html** file to reflect the current ip address.

2. Create a Dockerfile for the application

3. Next, build the image (tagged "nodeapp:1.1") from the Dockerfile

    ```
    docker build -t nodeapp:1.1 .
    ```

![dk images](https://github.com/uedwinc/NodeApp-with-Docker/blob/main/images/dk%20images.png)

3. Now, let's create a docker compose file as "yaml"

- Here, you define:
    - Version; of the docker compose
    - Services; these includes:
        - nodeapp service
        - mongodb service
        - mongo-express service
    - Volumes; a named volume to persist data in the database
    - Networks

- Use `docker volume ls` to see all available volumes

- Use `docker volume inspect volume-name` to see details

- To know the default volume space on the container, use `docker inspect mongodb`

![volume loc cont](https://github.com/uedwinc/NodeApp-with-Docker/blob/main/images/volume%20loc%20cont.png)

- The volume space on the container is /data/db


4. Let's start up our containers using the docker compose file

    ```
    docker compose -f node_mongo.yaml up -d
    ```

    - "node-mongo.yaml" is the name I gave my docker compose file.

![compose](https://github.com/uedwinc/NodeApp-with-Docker/blob/main/images/compose.png)

5. On the web browser, we can access our database. Create a "database name" and "collection name".

![compose db](https://github.com/uedwinc/NodeApp-with-Docker/blob/main/images/compose%20db.png)

6. Application listening on port 3000

![nodeapp](https://github.com/uedwinc/NodeApp-with-Docker/blob/main/images/nodeapp.png)

7. Application connected to database

![edit nodeapp](https://github.com/uedwinc/NodeApp-with-Docker/blob/main/images/edit%20nodeapp.png)

![nodeapp db](https://github.com/uedwinc/NodeApp-with-Docker/blob/main/images/nodeapp%20db.png)

8. Application volume space

![volume space](https://github.com/uedwinc/NodeApp-with-Docker/blob/main/images/volume%20space.png)

![volume inspect](https://github.com/uedwinc/NodeApp-with-Docker/blob/main/images/volume%20inspect.png)

![volume mounts](https://github.com/uedwinc/NodeApp-with-Docker/blob/main/images/volume%20mounts.png)

---

## Pushing the Docker Image to Elastic Container Registry (ECR)

1. Go to aws and create an ECR repository. Make sure it has same name as the name of the image to be pushed into it.

![ecr](https://github.com/uedwinc/NodeApp-with-Docker/blob/main/images/ecr.png)

- Note that you need to have installed "aws cli" and configured with access and secret key inorder to push to ECR.

2. After creating your repository on ECR, use the "view push commands" option to see steps on how to push to ECR

![push commands](https://github.com/uedwinc/NodeApp-with-Docker/blob/main/images/push%20commands.png)

- Skip step 2 as we already have a built image

- Now tag following next instruction (use your own image tag)

![dk tag](https://github.com/uedwinc/NodeApp-with-Docker/blob/main/images/dk%20tag.png)

- Now push

```
docker push name-of-ecr-repository-image:tag
```

![dk push1](https://github.com/uedwinc/NodeApp-with-Docker/blob/main/images/dk%20push1.png)

![dk push2](https://github.com/uedwinc/NodeApp-with-Docker/blob/main/images/dk%20push2.png)

- Result of ECR scan

![scan results](https://github.com/uedwinc/NodeApp-with-Docker/blob/main/images/scan%20results.png)

3. We can copy the URI of this image as it is a copy of the original image and use it to push into other repositories or start our application from this image.

---

## Pushing the Docker Image to Docker Hub

1. First got to hub.docker.com and sign in or create an account if you don't have one.

2. `docker login` to authenticate into docker using your username and password

3. Now, tag the image

```
docker tag nodeapp:dev uedwinc/nodeapp:dev
```

![tagged](https://github.com/uedwinc/NodeApp-with-Docker/blob/main/images/tagged.png)

4. Push to Dockerhub

```
docker push uedwinc/nodeapp:dev
```

![dk pushed1](https://github.com/uedwinc/NodeApp-with-Docker/blob/main/images/dk%20pushed1.png)

![dk pushed2](https://github.com/uedwinc/NodeApp-with-Docker/blob/main/images/dk%20pushed2.png)

---

## Pushing the Docker Image to Nexus Repository

1. First, stop all running containers

2. mongo-express runs on port 8081, which is the default port for nexus, so we need to change our host port for mongo-express on the yaml docker compose file. Open this port up on aws security group. I used 8082.

3. Launch nexus instance on AWS

4. Set-up nexus following this: (link)

5. Start nexus and access the web console using the ip address and port

```
/bin/nexus start
```

![start nexus](https://github.com/uedwinc/NodeApp-with-Docker/blob/main/images/start%20nexus.png)

6. Sign into nexus using admin username and password

![sign in](https://github.com/uedwinc/NodeApp-with-Docker/blob/main/images/sign%20in.png)

7. Next, create a docker(hosted) repository on nexus web console.

![docker hosted](https://github.com/uedwinc/NodeApp-with-Docker/blob/main/images/docker%20hosted.png)

8. Create a user role with 'nx-repository-view-docker...*' privileges

![role](https://github.com/uedwinc/NodeApp-with-Docker/blob/main/images/role.png)

9. Create a user and attach this role to the user

![user](https://github.com/uedwinc/NodeApp-with-Docker/blob/main/images/user.png)

- Map a port, say 8083, to the docker(hosted) repository on nexus

![map](https://github.com/uedwinc/NodeApp-with-Docker/blob/main/images/map.png)

- Go to "Realms" on the nexus web console and configure access tokens by adding the "Docker Bearer Token Realm" to "Active"

![realms](https://github.com/uedwinc/NodeApp-with-Docker/blob/main/images/realms.png)

10. On the docker server, we need to authenticate with nexus repository

11. By default, docker does not push to non-encripted http repositories. It needs to be https. We need to find a way around this.

We can follow the instructions here to deploy plain http (https://docs.docker.com/registry/insecure/#deploy-a-plain-http-registry)

![insecure](https://github.com/uedwinc/NodeApp-with-Docker/blob/main/images/insecure.png)

- Restart docker service

```
sudo service docker restart
```

12. Now, login to the nexus repository url from docker using your username and password

```
docker login 13.58.249.65:8083
```

13. After authentication, we need to tag our image for nexus push

```
docker tag nodeapp:dev 13.58.249.65:8083/nodeapp:dev
```

14. Now push

```
docker push 13.58.249.65:8083/nodeapp:dev
```

![push1](https://github.com/uedwinc/NodeApp-with-Docker/blob/main/images/push1.png)

![push2](https://github.com/uedwinc/NodeApp-with-Docker/blob/main/images/push2.png)

---

### Obtaining the Artifact

```
curl -u docker-user:password$ -X GET 'http://13.58.249.65:8081/service/rest/v1/components?repository=docker-nodeapp'
```
