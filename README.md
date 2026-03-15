# fork team project
1. go to -> https://github.com/arielBengayev/first-project
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
