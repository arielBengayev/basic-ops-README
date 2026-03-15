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
SELECT 'ariel', 1 from dual
```
press on run current
