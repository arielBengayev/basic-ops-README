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
