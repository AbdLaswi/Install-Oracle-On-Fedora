# Introduction

first of all, you need to know that Fedora does not support the Oracle database, which means you can't install the oracle database on Fedora directly, there are two ways to install Oracle on your machine, either you install it by using **Virtual Machine (VM)** or by using **Docker**, in this documentation, we will use **Docker**, but feel free to install it by installing **VM**.

**IMPORTANT**: this documant is inspired by
- [Link](https://github.com/oracle/docker-images/blob/main/OracleDatabase/SingleInstance/README.md) 
- [Link](https://marschall.github.io/2020/04/12/oracle-docker-container.html)


# Getting started with installing Docker

 you can install Docker by following this **[link](https://docs.docker.com/engine/install/fedora/)**.

# Install Docker Images From Oracle 

first, you need to clone this **[repo](https://github.com/oracle/docker-images)**.
  
or you can follow these commands.
  
open your terminal then run this command below **make sure you are in Home directory**.

      git clone https://github.com/oracle/docker-images
 
once it's done, use this one below. 

      cd docker-images/OracleDatabase/SingleInstance/dockerfiles
 
 this repo has these versions.

   **Oracle database:**
 - 11.2.0.2
 - 12.1.0.2
 - 12.2.0.1
 - 18.3.0 
 - 18.4.0  
 - 19.3.0

# Installation 

in this document, we are going to install **19.3.0**, feel free to install any Version you want but first, you need to install a zip file called **LINUX.X64_{version number}_db_home.zip** 

in our case we are going to install **LINUX.X64_193000_db_home.zip**, you can find it on this **[link](https://www.oracle.com/database/technologies/oracle-database-software-downloads.html#19c)** **make sure you are in Home/{username}/Download directory**

make sure you've installed the zip file. 

we almost done, after installing the **LINUX.X64_193000_db_home.zip** file, follow run this command below 

      sudo cp ~/Download/LINUX.X64_193000_db_home.zip ~/dockerimages/OracleDatabase/SingleInstance/dockerfiles/19.3.0
  
if the command didn't work all what you need is to run 
`sudo cp` path file where you downloaded **LINUX.X64_193000_db_home.zip** path file where cloned Docker-images, but make sure you path ends with the version that you are going to install.
  
  so now run this command below

      cd ~/docker-images/OracleDatabase/SingleInstance/dockerfiles
 
 now we are ready to create our contanier image by runnung this command 


       ./buildContainerImage.sh -s -v 19.3.0 -o '--build-arg SLIMMING=false'


if you want to install a different version all that you need to make sure that you installed **LINUX.X64_{version number}_db_home.zip** and copied it to the version file that you wanted to install

# Configration  

now we need to build our configration file **make sure you are in Home directory**

      mkdir sql_configration
 
 then go to sql_configration directory 
 
      cd sql_configration
 let's start with user information
 after that, we want to create three files to build setup configration
 
      cat > 01_users.sql
 
inside 01_users.sql file 

      
          ALTER SESSION SET CONTAINER = database_name;
          CREATE USER User_name IDENTIFIED BY "your-password";

now let's build permissions file


      cat > 02_permissions.sql 
 
 
 inside 02_permissions.sql file:
      
         ALTER SESSION SET CONTAINER = database_name;
        
         GRANT CONNECT TO User_name CONTAINER=CURRENT;
         GRANT CREATE SESSION TO User_name CONTAINER=CURRENT;
         GRANT RESOURCE TO User_name CONTAINER=CURRENT;

         ALTER USER User_name QUOTA 100M ON USERS;
         
  
 finally
 
 
      cat > 03_objects.sql
      
 
 inside 03_objects.sql file:
 
  
       ALTER SESSION SET CONTAINER = database_name;  
       ALTER SESSION SET CURRENT_SCHEMA = User_name;
           
       CREATE TABLE test_table (
        id  NUMBER(5) NOT NULL PRIMARY KEY
        );

# Run Docker CONTAINER
 
      docker run --name {CONTAINER_name} -p 1521:1521 -p 5500:5500 --shm-size=1g -v /home/{username}/sql_configration:/opt/oracle/scripts/startup oracle/database:19.3.0-se2

in case the server does not work as expected 

  run this command 
    
      docker ps

then copy **CONTAINER ID**
  
      docker logs {CONTAINER ID} -f
    
# Issues

 in case you followed every step and you faced some issues, you can check these Links
 
- [Link](https://github.com/oracle/docker-images/blob/main/OracleDatabase/SingleInstance/README.md) 
- [Link](https://marschall.github.io/2020/04/12/oracle-docker-container.html)

# Extra 
 also you can pull Oracle linux 7 or 8 [here](https://hub.docker.com/_/oraclelinux?tab=tags&page=1&ordering=last_updated) 
 and build everything manually. 
