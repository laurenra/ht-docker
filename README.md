#Run MyBYU (uPortal) in Docker containers
This builds 2 Docker images that are customized to run BYU's version of uPortal called MyBYU.  One has Java and a Tomcat server and the other has an Apache web server with SSL that acts as a reverse proxy to forward requests to the Tomcat server. They are attached to the same Docker network.

For security reasons, the uPortal .war and .jar files, database connection settings, and digital certificates are not contained in the Docker images. Get those from a developer (see the mybyu-docker-private tar file below).

##Build container and run it with docker-compose command
This assumes that you have the compiled .jar and .war files from the MyBYU project and that you have Docker installed. See [Get Docker](https://www.docker.com/products/overview) at [docker.com](https://www.docker.com/) for how to install Docker.

###Easiest way to build the containers

1. **git clone** this project, for example:

    ```
    git clone https://github.com/byu-oit-appdev/mybyu-docker.git
    ```
    
2. Get the **mybyu-docker-private** tar file from a developer, copy it to the project root, **/mybyu-docker**, and uncompress it there. This file has all the private setup files and digital certificates.

    ```
    tar xvf mybyu-docker-private.tar
    ```

3. Copy the compiled MyBYU .jar and .war files from your project to the **app-server/wars** directory. Use the included **get-mybyu-files** script in the **app-server** directory to simplify this. Before running the get-mybyu-files script modify the BASEDIR variable in the script to point to your MyBYU project directory.

    ```
    cd app-server/wars
    ../get-mybyu-files all
    ```

    _These are the .jar files required when the container and Tomcat **start** in order for MyBYU to work_:
    * ccpp.jar
    * person-directory-api.jar
    * pluto-container-api.jar
    * pluto-container-driver-api.jar
    * pluto-taglib.jar
    * portlet-api.jar

4. If you use a VPN to access the database servers, make sure it's running before starting the Docker container.

5. Run docker-compose _**in the project root directory**_ (where the .yml file is) to get the parent images and build the mybyu/tomcat and mybyu/apache images from them using the configuration in the Docker files:

    ```
    docker-compose up -d
    ```
    
    (The "-d" option runs the container as a daemon in the background.)
    
6. Log in to the shell of the app server container, the one that's running Tomcat:

    ```
    docker exec -it mybyuapp /bin/sh
    ```
    
    (If you'd like you can run the setprompt script in the container to change the appearance of the prompt and create an alias for ll (ls -al).)
     
    ```
    source setprompt
    ```
    
7. Copy the .war files to the **/opt/tomcat/webapps** directory in the container using the included **mybyu** script. For example, to copy a minimal set of .war files to run MyBYU:

    ```
    mybyu min
    ```

    You can also do this manually by copying specific .war files from **/usr/share/wars** inside the container.

    Or to copy all the .jar and .war files:
    ```
    mybyu all
    ```

    Run the mybyu script with no options to see a list of options.
    
    It will take a minute or two for Tomcat to install all of the .war files. You can check progress by viewing the catalina*.log file and the uPortal.log file (after it's created), for example:
    
    ```
    tail -f /opt/tomcat/logs/catalina.2016-11-21.log
    tail -f /opt/tomcat/logs/uPortal.log
    ```
    
    Use **Ctrl+c** to break out of "tail -f".
    
    You can access MyBYU from a web browser at [https://localhost.byu.edu/uPortal](https://localhost.byu.edu/uPortal).
    
    When done with MyBYU, exit the running container's shell with **exit** or **Ctrl+d**.  
    
###Stop the containers

From the directory that contains the **docker-compose.yml** file:
```
docker-compose stop
```
   
Or to stop the individual containers one at a time from anywhere:
   
```
docker stop mybyuweb
docker stop mybyuapp
``` 

###Start the containers

From the directory that contains the **docker-compose.yml** file:
```
docker-compose start
```
   
Or from anywhere:
   
```
docker start mybyuapp
docker start mybyuweb
```

###Delete the containers

From the directory that contains the **docker-compose.yml** file:
```
docker-compose down
```

Or from anywhere once the container is stopped:
```
docker rm mybyuapp
docker rm mybyuweb
```

###Common Docker commands

| Command                                   | Description                      |
| ----------------------------------------- | -------------------------------- |
| docker ps -a                              | Show all docker containers       |
| docker images                             | Show all docker images           |
| docker start {image id/name}              | Start specified container        |
| docker stop {container id/name}           | Stop specified container         |
| docker rm {container id/name}             | Remove specified container       |
| docker rmi {image id/name}                | Remove specified image           |
| docker run {options}                      | Build a container and run it     |
| docker exec {container id/name} {command} | Run the command in the container |
| docker build {options}                    | Build an image                   |
| docker help                               | List docker commands             |
| docker {command} --help                   | Show help for specific command   |

###Common Docker Compose commands

These commands must be run in the directory where docker-compose.yml is located.

| Command                         | Description                                      |
| ------------------------------- | ------------------------------------------------ |
| docker-compose build            | Build image(s)                                   |
| docker-compose up               | Build image(s), start container(s)               |
| docker-compose up -d            | Build image(s), start container(s) in background |
| docker-compose start            | Start container(s)                               |
| docker-compose stop             | Stop container(s)                                |
| docker-compose down             | Stop containers and remove them                  |
| docker-compose help             | List docker-compose commands                     |
| docker-compose {command} --help | Show help for specific command                   |

Note: any changes you make inside a container will be still be available after restarting the container. They will disappear if you delete the container.

## Notes about sensitive private setup data
There are a few files that reside outside the Docker containers on the host computer including digital certificates and database connections. They are in the **mybyu-docker-private** tar file and are copied to the appropriate directories when you uncompress it from the project root.
