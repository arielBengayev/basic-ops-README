# WSL(linux)
check if ubuntu installed, if not

open cmd and run
```
wsl --install
```

---

# fork team project
1. go to -> https://github.com/arielBengayev/ops-basic
2. press on fork
3. choose a owner and repo name
4. press on create fork
5. now the repo forked to your github

---

# create a db container
open a linux terminal and run
```
docker --version
```
if not have
1. open docker desktop
2. go to settings -> resources -> wsl integration
3. enable ubuntu
4. press apply & restart

run to create the container
```
sudo docker run -d \
    -e MYSQL_ROOT_PASSWORD=Unix11 \
    -e MYSQL_DATABASE=students \
    -e MYSQL_USER=students \
    -e MYSQL_PASSWORD=Unix11 \
    -p 3306:3306 \
    -v mysql-data:/var/lib/mysql \
    mysql:8.0
```
run to make sure the container is up
```
sudo docker ps
```

---

# connect to db 
1. open tablePlus
2. create new connection
3. choose mySQL
4. name -> students-data
5. Host 127.0.0.1
6. user -> students
7. password -> Unix11
8. database -> students
9. press test and make sure the connection is ok
10. now you can connect to the db

# test
press on sql 
```
SELECT 'your name', 1 from dual
```
press on run current

---

# SSH key setup
1. run in terminal and press enter for all
```
ssh-keygen
```
```
ls -la ~/.ssh
```
```
cat ~/.ssh/yourId.pub
```
2. copy your key and save
3. go to -> https://github.com/settings/keys
4. press on new SSH key
5. past your key and press add SSH key

---

# connect project to db
change src/main/resources/application.properties to
```
spring.datasource.url=jdbc:mysql://mysql:3306/students
spring.datasource.username=students
spring.datasource.password=Unix11
```

---

# clone the project to ubuntu
1. go to main repo page
2. press on code
3. copy the SSH
4. run in terminal
```
git clone your SSH url
```
5. choose yes

to delete the clone folder you can run
```
rm -r folder_name
```

---

# maven build
1. go to the project folder
```
cd ops-basic
```
2. in ops-basic folder install maven
```
sudo apt update
```
```
sudo apt install maven -y
```
```
mvn --version
```
3. in ops-basic folder run
```
mvn clean install
```
new folder "target" created

to see the folder run
```
cd target
```
```
ll
```
go back to main folder
```
cd ..
```

---

# dockerhub security token setup
1. go to dockerhub and sign in
2. press on your user button and go to settings
3. press on Personal access tokens -> generate new token
4. in access premition choose -> Read, Write, Delete
5. press on generate, copy the token and save

---

# dockerize the project
make sure the container is up
```
docker ps
```
if not run
```
sudo docker run -d \
    -e MYSQL_ROOT_PASSWORD=Unix11 \
    -e MYSQL_DATABASE=students \
    -e MYSQL_USER=students \
    -e MYSQL_PASSWORD=Unix11 \
    -p 3306:3306 \
    -v mysql-data:/var/lib/mysql \
    mysql:8.0
```
docker build
```
sudo docker build . -t backend
```
login to docker
```
sudo docker login
```
enter your username and password or past your dockerhub token
```
sudo docker tag backend <dockerhub username>/backend
```
```
sudo docker push <dockerhub username>/backend
```

---

# docker-compose
upddate project
```
git pull
```
create docker-compose file
```
echo "
version: "3"
services:
  appserver:
    container_name: server2
    image: <dockerhub username>/backend:latest
    ports:
      - 8080:8080
    depends_on:
      - mysql
  mysql:
    image: mysql:8.0
    environment:
      MYSQL_ROOT_PASSWORD: Unix11
      MYSQL_DATABASE: students
      MYSQL_USER: students
      MYSQL_PASSWORD: Unix11
    ports:
      - 3306:3306
    volumes:
      - ./mysql-data:/var/lib/mysql
    privileged: true
" >>  docker-compose.yml
```
to see the file
```
cat docker-compose.yml
```
to change the file
```
nano docker-compose.yml
```
to save the changes -> ctrl + o -> enter

to exit -> ctrl + x

---

# push the changes to github
1. to see the changes
```
git status
```
2. add to git
```
git add .
```
```
git status
```
3. commit and push
```
git commit -m "with docker-compose"
```
```
git push
```
now you can see the changes on github

---

# run the docker-compose
kill the first container
```
docker ps
```
copy the container id and run
```
docker kill container id
```
now you can run the docker compose
```
docker-compose up -d
```
go to -> http://localhost:8080/swagger-ui.html#

---

# docker automation
1. go to the project repo
2. go to repo settings -> secrets and variables -> actions
3. press on new repository secret
4. in name
```
DOCKERHUB_USERNAME
```
```
DOCKERHUB_TOKEN
```
5. in secret -> your dockerhub username and dockerhub token
6. go back to repo and press on actions
7. choose Docker image and change the file name to -> build.yml
8. change the file to
```
name: Build and Push

on:
  push:
    branches:
      - master

env:
  APP_VERSION: v1.0.${{ github.run_number }}

jobs:
  build:
    name: Build & Push
    runs-on: ubuntu-latest
    steps:
      - name: checkout
        uses: actions/checkout@v4
      - name: Set up JDK 11
        uses: actions/setup-java@v4
        with:
          java-version: '11'
          distribution: 'temurin'
      - name: Build and analyze
        env:
          GITHUB_TOKEN: ${{ secrets.ACCESS_TOKEN }}
        run: mvn clean install
      - name: Set up QEMU
        uses: docker/setup-qemu-action@v3
      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v3
      - name: Login to DockerHub
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}
      - name: Build and push
        id: docker_build
        uses: docker/build-push-action@v5
        with:
          context: .
          push: true
          tags: ${{ secrets.DOCKERHUB_USERNAME }}/backend:${{ env.APP_VERSION }}
```
9. press on commit changes
10. go to controller/studentController in the repo
11. change getOneStudent to -> getOneStudent1
12. go to actions in the repo, now you can see the new building
13. go to dockerhub and see if new tag added

---

# front dockerize
1. go to -> https://github.com/arielBengayev/ops-basic-angular

    fork to your github account

2. go to -> src/environments/environment.prod.ts

    change the url
```
'http://localhost:8080/api'
```
3. go to -> src/environments/environment.ts

    change the url
```
'http://localhost:8080/api'
```
4. create new file in main folder
```
Dockerfile
```
copy to Dockerfile
```
# build stage
FROM node:16 AS build
WORKDIR /app

COPY package*.json ./
RUN npm install --legacy-peer-deps

COPY . .
RUN npm run build --prod

# serve stage
FROM nginx:alpine

COPY nginx.conf /etc/nginx/conf.d/default.conf
COPY --from=build /app/dist/webapp /usr/share/nginx/html
```
5. clone the front repo
```
git clone <ssh url>
```
6. open the front project in terminal
```
cd project folder
```
7. docker build, tag and push
```
docker build -t <dockerhub username>/frontend:latest .
```
```
docker push <dockerhub username>/frontend:latest
```
go to dockerhub and see the new image

8. go to the backend project -> docker-compose.yml

    add the front service
```
    frontend:
    image: <dockerhub username>/frontend:latest
    ports:
      - "4200:80"
    depends_on:
      - appserver 
```
9. open the backend project on terminal

    update the project
```
git pull
```

10. run the project

```
docker-compose up -d
```
go to -> http://localhost:4200

11. go to -> http://localhost:8080/swagger-ui.html#

    press on -> jwt-authentication-controller -> /api/user

    create a user

12. go to -> http://localhost:4200

    enter your username and pasword and login

---

# front automation
1. go to the project repo
2. go to repo settings -> secrets and variables -> actions
3. press on new repository secret
    in name
```
DOCKERHUB_USERNAME
```
```
DOCKERHUB_TOKEN
```
in secret -> your dockerhub username and dockerhub token 

4. go back to repo and press on actions
5. choose Docker image and change the file name to -> build.yml
6. change the file to
```
name: Build Frontend

on:
  push:
    branches:
      - main

env:
  APP_VERSION: v1.0.${{ github.run_number }}

jobs:
  build:
    name: Build Frontend Image
    runs-on: ubuntu-latest
    steps:
      - name: checkout
        uses: actions/checkout@v4
      - name: Set up QEMU
        uses: docker/setup-qemu-action@v3
      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v3
      - name: Login to DockerHub
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}
      - name: Build and push
        uses: docker/build-push-action@v5
        with:
          context: .
          push: true
          tags: |
            ${{ secrets.DOCKERHUB_USERNAME }}/frontend:latest
            ${{ secrets.DOCKERHUB_USERNAME }}/frontend:${{ env.APP_VERSION }}
```
7. go to -> src/app/pages/login/login.component.html
   
   change some html and save changes

8. stop the docker-compose, run again and go to -> http://localhost:4200

   now you can see the changes

   if not see any change press -> ctrl + shift + r
   
---

# gitlab account & repo
1. go to -> https://gitlab.com
2. creat account (with username and password)

3. create new repo
   
    1. press on +
    2. choose new project
    3. choose Create blank project
    4. in Project URL pick a namespace
    5. without readme!!
    6. press on create project

4. clone the repo
```
git clone <https url>
```

5. copy all files to the repo (not the .github folder)
6. push to the repo
```
git add .
```
```
git commit -m "with project"
```
```
git push
```
7. go to gitlab and see all files

---

# gitlab automation
1. in repo go to Settings → CI/CD → Variables
2. add keys and values
```
DOCKERHUB_USERNAME
```
```
DOCKERHUB_TOKEN
```
3. create new file
```
.gitlab-ci.yml
```
4. copy to .gitlab-ci.yml

backend
```
stages:
  - build
  - docker

variables:
  APP_VERSION: "v1.0.${CI_PIPELINE_IID}"

build_app:
  stage: build
  image: maven:3.9-eclipse-temurin-11

  script:
    - mvn clean install

  artifacts:
    paths:
      - target/

docker_build_push:
  stage: docker
  image: docker:latest

  services:
    - docker:dind

  before_script:
    - docker login -u "$DOCKERHUB_USERNAME" -p "$DOCKERHUB_TOKEN"

  script:
    - docker build -t $DOCKERHUB_USERNAME/backend:$APP_VERSION .
    - docker push $DOCKERHUB_USERNAME/backend:$APP_VERSION

  only:
    - main
```

frontend
```
stages:
  - docker

variables:
  APP_VERSION: "v1.0.${CI_PIPELINE_IID}"

docker_build_push:
  stage: docker

  image: docker:latest

  services:
    - docker:dind

  before_script:
    - docker login -u "$DOCKERHUB_USERNAME" -p "$DOCKERHUB_TOKEN"

  script:
    - docker build -t $DOCKERHUB_USERNAME/frontend:latest .
    - docker build -t $DOCKERHUB_USERNAME/frontend:$APP_VERSION .
    - docker push $DOCKERHUB_USERNAME/frontend:latest
    - docker push $DOCKERHUB_USERNAME/frontend:$APP_VERSION

  only:
    - main
```
5. in repo go to -> build -> pipeline and see the pipeline
6. go to dockerhub and see the new image

---

# kubernetes
1. install minikube and kubectl from cmd

   windows
   ```
   winget install Kubernetes.minikube
   ```
   mac
   ```
   brew install minikube
   ```

   check version
   ```
   minikube version
   ```
   ```
   kubectl version
   ```

2. start a cluster

   ```
   minikube start --driver docker
   ```

   to see the cluster status
   ```
   minikube status
   ```

   to see all nodes
   ```
   kubectl get node
   ```
3. create new folder and open in vs code
   ```
   K8S
   ```
   create new file - configmap
   ```
   mongo-config.yml
   ```
   copy to the file
   ```
   apiVersion: v1
   kind: ConfigMap
   metadata:
     name: mongo-config
   data:
     mongo-url: mongo-service
   ```
   create new file - secret
   ```
   mongo-secret.yml
   ```
   open ubuntu and run
   ```
   echo -n mongouser | base64
   ```
   ```
   echo -n mongopassword | base64
   ```
   copy to the file
   ```
    apiVersion: v1
    kind: Secret
    metadata:
      name: mongo-secret
    type: Opaque
    data:
      mongo-user: hashed user
      mongo-password: hashed password
   ```
   create new file - mongo deployment and service
   ```
   mongo.yml
   ```
   copy to the file
   ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: mongo-deployment
      labels:
        app: mongo
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: mongo
      template:
        metadata:
          labels:
            app: mongo
        spec:
          containers:
          - name: mongodb
            image: mongo:5.0
            ports:
            - containerPort: 27017
            env:
            - name: MONGO_INITDB_ROOT_USERNAME
              valueFrom:
                secretKeyRef:
                  name: mongo-secret
                  key: mongo-user
            - name: MONGO_INITDB_ROOT_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mongo-secret
                  key: mongo-password     
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: mongo-service
    spec:
      selector:
        app: mongo
      ports:
        - protocol: TCP
          port: 27017
          targetPort: 27017
   ```
   create new file - app deployment and service
   ```
   webapp.yml
   ```
   copy to the file
   ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: webapp-deployment
      labels:
        app: webapp
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: webapp
      template:
        metadata:
          labels:
            app: webapp
        spec:
          containers:
          - name: webapp
            image: nanajanashia/k8s-demo-app:v1.0
            ports:
            - containerPort: 3001
            env:
            - name: USER_NAME
              valueFrom:
                secretKeyRef:
                  name: mongo-secret
                  key: mongo-user
            - name: USER_PWD
              valueFrom:
                secretKeyRef:
                  name: mongo-secret
                  key: mongo-password 
            - name: BD_URL
              valueFrom:
                configMapKeyRef:
                  name: mongo-config
                  key: mongo-url
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: webapp-service
    spec:
      type: NodePort
      selector:
        app: webapp
      ports:
        - protocol: TCP
          port: 3001
          targetPort: 3001
          nodePort: 30100
   ```
4. create in kubernetes
    
   open a terminal and run
   ```
   kubectl apply -f mongo-config.yml
   ```
   ```
   kubectl apply -f mongo-secret.yml
   ```
   ```
   kubectl apply -f mongo.yml
   ```
   ```
   kubectl apply -f webapp.yml
   ```

   to see all pods/services
   ```
   kubectl get all 
   ```
   to see the webapp
   ```
   minikube service webapp-service
   ```
   to stop the cluster
   ```
   minikube stop
   ```
