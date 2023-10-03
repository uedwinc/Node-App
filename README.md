## Profile App with Database

Deploying a nodejs application with mongodb database.

This app shows a simple user profile app set-up using 
- index.html with pure js and css styles
- nodejs backend with express module
- mongodb for data storage
Then, there's mongo express to serve as UI for mongodb

All components are docker-based


### Tools and Packages



### Running the Database Containers

- First, we clone the git repository to be used

1. You can create a docker network to link resources between different docker containers

    ```docker network create mongo-network```

    ```docker network ls``` to see the list of networks

In case you want to remove a network, use: ```docker network rm name-of-network```

2. Next, we run the containers using the official mongo and mongo-express images following directives from dockerhub

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
![mongo ps](link)

- Now, we can access the mongo ui on the browser using ip address and port
![mongo ui](link)

- We create a database called 'user-account', and a table / collection called 'users'
![user-account](link)
![users](link)


### Starting the NodeJS Application Locally

1. First, navigate to the app directory.

2. Go to **index.html** and map your own ip address to the fetch url

3. Since we want to run the application locally, go into **server.js** and make sure you configure "mongoClient.connect" to use "mongourlLocal"

4. Then, we need to run ```npm install && npm audit fix```

- Use ```apt install npm``` if not already installed

5. Now, run ```node server.js``` to start the application

![node listen](link)

- First, we need to open port 3000 up in our security group

6. Let's access the webpage on the ip address and the listening port (3000)

    http://localhost:3000
    
![page](link)

7. Let's edit the profile page

![profile edit](link)

- We can see this reflect on the database

![info mongo](link)


## Using Docker Compose

1. Go into **server.js** in the app directory and configure "mongoClient.connect" to use "mongoUrlDockerCompose". If necessary, edit the **index.html** file to reflect the current ip address.

2. We need to create a Dockerfile for our application

3. Next, we build the image (tagged "nodeapp:1.1") from our Dockerfile

    docker build -t nodeapp:1.1 .

![dk images](link)

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
- To know the default volume space on the container, use `docker inspect mongodb`
- The volume space on the container is /data/db


4. Let's start up our containers using the docker compose file

    docker compose -f node-mongo.yaml up -d

    - "node-mongo.yaml" is the name I gave my docker compose file.

![compose](link)

5. On the web browser, we can access our database. Create a "database name" and "collection name".

![compose db](link)

6. 




