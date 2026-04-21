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
```
SSH_KEY
```
5. in secret -> your dockerhub username, tocken and ubuntu ssh key
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
        uses: actions/checkout@v2
      - name: Set up JDK 11
        uses: actions/setup-java@v2
        with:
          java-version: '11'
          distribution: 'temurin'
      - name: Build and analyze
        env:
          GITHUB_TOKEN: ${{ secrets.ACCESS_TOKEN }}
        run: mvn clean install
      - name: Set up QEMU
        uses: docker/setup-qemu-action@v1
      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v1
      - name: Login to DockerHub
        uses: docker/login-action@v1
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}
      - name: Build and push
        id: docker_build
        uses: docker/build-push-action@v2
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
