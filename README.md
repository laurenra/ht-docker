# Run HT app in Docker containers
This builds 2 Docker images that are customized to run HT apps.  One has Java and a Tomcat server and the other has an Apache web server with SSL that acts as a reverse proxy to forward requests to the Tomcat server. They are attached to the same Docker network.

For security reasons, the uPortal .war and .jar files, database connection settings, and digital certificates are not contained in the Docker images. Get those from a developer (see the private-ht-docker tar file below).

## Build container and run it with docker-compose command
This assumes that you have the compiled .jar and .war files from the HT project and that you have Docker installed. See [Get Docker](https://www.docker.com/products/overview) at [docker.com](https://www.docker.com/) for how to install Docker.

### Easiest way to build the containers

1. **git clone** this project, for example:

    ```
    git clone https://github.com/laurenra/ht-docker.git
    ```
    
2. Get the **private-ht-docker** tar file from a developer, copy it to the project root, **/ht-docker**, and uncompress it there. This file has all the private setup files and digital certificates.

    ```
    tar xvf private-ht-docker.tar
    ```

3. Copy the compiled .jar and .war files from your project to the **app-server/wars** directory.

    ```
    cd app-server/wars
    cp /path/to/war/files .
    ```


4. If you use a VPN to access any database servers defined in Tomcat with JNDI, make sure it's running before starting the Docker container.

5. Run docker-compose _**in the project root directory**_ (where the .yml file is) to get the parent images and build the ht/tomcat and ht/apache images from them using the configuration in the Docker files:

    ```
    docker-compose up -d
    ```
    
    (The "-d" option runs the container as a daemon in the background.)
    
6. Log in to the shell of the app server container, the one that's running Tomcat:

    ```
    docker exec -it htapp /bin/sh
    ```
    
    (If you'd like you can run the setprompt script in the container to change the appearance of the prompt and create an alias for ll (ls -al).)
     
    ```
    source setprompt
    ```
    
7. Copy the .war files from the **/usr/share/wars** directory to the **/opt/tomcat/webapps** directory in the container. For example:

    ```
    cp /usr/share/wars/my-app.war /opt/tomcat/webapps/
    ```

    It will take a minute or two for Tomcat to install all of the .war files. You can check progress by viewing the catalina*.log file and your application log file (after it's created), for example:
    
    ```
    tail -f /opt/tomcat/logs/catalina.2016-11-21.log
    tail -f /opt/tomcat/logs/my-app.log
    ```
    
    Use **Ctrl+c** to break out of "tail -f".
    
    You can access the Tomcat web app from a web browser at [https://localhost.byu.edu](https://localhost.byu.edu).
    
    When done with the web application, exit the running container's shell with **exit** or **Ctrl+d**.
    
### Stop the containers

From the directory that contains the **docker-compose.yml** file (or any of its sub-directories):
```
docker-compose stop
```
   
Or to stop the individual containers one at a time from anywhere:
   
```
docker stop htweb
docker stop htapp
``` 

### Start the containers

From the directory that contains the **docker-compose.yml** file (or any of its sub-directories):
```
docker-compose start
```
   
Or from anywhere:
   
```
docker start htapp
docker start htweb
```

### Delete the containers

From the directory that contains the **docker-compose.yml** file:
```
docker-compose down
```

Or from anywhere once the container is stopped:
```
docker rm htapp
docker rm htweb
```

### Common Docker commands

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

### Common Docker Compose commands

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
There are a few files that reside outside the Docker containers on the host computer including digital certificates and database connections. They are in the **private-ht-docker** tar file and are copied to the appropriate directories when you uncompress it from the project root.
