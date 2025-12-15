# A Flask App with MySQL Docker Setup :-  
This is a simple Flask app that interacts with a MySQL database. The app allows users to submit messages, which are then stored in the database and displayed on the frontend.  

Major steps:
1. Launch an AWS EC2 Ubuntu Instance
2. Update the system 
   - sudo apt update && sudo apt upgrade -y
3. Install Docker 
   - sudo apt install docker.io
4. Give user permissions to docker 
   - sudo usermod -aG docker $USER && newgrp docker 
5. We have Dockerfile in the folder.
6. Build a docker image 
   - docker build . -t flaskapp
7. Try to create & run the container 
   - docker run -d -p 5000:5000 flaskapp:latest 
8. You can see the app page on http://localhost:5000 but, database container is not running and it's not connected.
9. Kill the current container and create new two container and connect them using docker network.
10. To connect two containers we use docker network.  Create a docker network named twotier
    - docker network create twotier
11.  Attach both the containers in the same network, so that they can communicate with each other\
i) MySQL container

docker run -d \
    --name mysql \
    -v mysql-data:/var/lib/mysql \
    --network=twotier \
    -e MYSQL_DATABASE=mydb \
    -e MYSQL_ROOT_PASSWORD=admin \
    -p 3306:3306 \
    mysql:5.7\
ii) Backend container

docker run -d \
    --name flaskapp \
    --network=twotier \
    -e MYSQL_HOST=mysql \
    -e MYSQL_USER=root \
    -e MYSQL_PASSWORD=admin \
    -e MYSQL_DB=mydb \
    -p 5000:5000 \
    flaskapp:latest
\  
Your MySQL configuration:  
MYSQL_HOST=mysql  
MYSQL_USER=your_username  
MYSQL_PASSWORD=your_password  
MYSQL_DB=your_database  

Access Flask app in your web browser:  
http://localhost:5000  
  
Create the messages table in your MySQL database:
Access the Mysql container using :  
- docker exec -it mysql_container_id bash  
- mysql -u root -p
- enter password
- show databases;
- use mydb;
- now create table named messages :
CREATE TABLE messages (  
    id INT AUTO_INCREMENT PRIMARY KEY,  
    message TEXT  
);  
- exit
- exit

Notes :-  
Make sure to replace placeholders (e.g., your_username, your_password, your_database) with your actual MySQL configuration.

This is a basic setup for demonstration purposes. In a production environment, you should follow best practices for security and performance.

Be cautious when executing SQL queries directly. Validate and sanitize user inputs to prevent vulnerabilities like SQL injection.

If you encounter issues, check Docker logs and error messages for troubleshooting.
