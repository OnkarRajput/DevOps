# Docker Basic to Advance 

## Below is the list with a short description of some of the most used Dockerfile instructions:

## ARG 
- This instruction allows you to define variables that can be passed at build-time. You can also set a default value.

## FROM 
- The base image for building a new image. This instruction must be the first non-comment instruction in the Dockerfile. The only exception from this rule is when you want to use a variable in the FROM argument. In this case, FROM can be preceded by one or more ARG instructions.
```FROM ubuntu:latest```

## LABEL 
- Used to add metadata to an image, such as description, version, author ..etc. You can specify more than one LABEL, and each LABEL instruction is a key-value pair.
```LABEL maintainer=someone@xyz.xyz"```

## RUN 
- The commands specified in this instruction will be executed during the build process. Each RUN instruction creates a new layer on top of the current image.
```RUN apt-get update && apt-get upgrade -y && apt-get install -y nginx && rm -rf /var/lib/apt/lists/* ```

## ADD 
 - Used to copy files and directories from the specified source to the specified destination on the docker image. The source can be local files or directories or an URL. If the source is a local tar archive, then it is automatically unpacked into the Docker image.

## COPY 
- Similar to ADD but the source can be only a local file or directory.

## ENV 
- This instruction allows you to define an environment variable.

## CMD 
- Used to specify a command that will be executed when you run a container. You can use only one CMD instruction in your Dockerfile.

## ENTRYPOINT 
- Similar to CMD, this instruction defines what command will be executed when running a container.

## WORKDIR 
- This directive sets the current working directory for the RUN, CMD, ENTRYPOINT, COPY, and ADD instructions.
  ```WORKDIR /home```
## USER 
- Set the username or UID to use when running any following RUN, CMD, ENTRYPOINT, COPY, and ADD instructions.
  ```USER docker```
## VOLUME 
- Enables you to mount a host machine directory to the container.

## EXPOSE 
- Used to specify the port on which the container listens at runtime.

- To, exclude files and directories from being added to the image, create a .dockerignore file in the context directory. The syntax of the .dockerignore is similar to the one of the Git’s .gitignore file.

# create a container in background, stop,start,detach container

- Usage:	docker container COMMAND

``` docker container list```

``` docker image ls```

``` docker container ls```    

``` docker container ls -a```

``` docker container rm 8e76cf05adee```

``` docker container rm $(docker container ls -aq)```

``` docker container run -d -it ubuntu /bin/bash ```

``` docker container run -it ubuntu /bin/bash ```

``` docker container stop 8e76cf05adee ```

``` docker container start 8e76cf05adee ```                     
- start stopped container

``` detach container "Ctrl+p+q" ```

 ## what's going on inside container
 ``` docker container top 8e76cf05adee ```                        
 - Display the running processes of a container
 
 ``` docker container stats ```                                    
 - Display a live stream of container(s) resource usage statistics
 
 ``` docker container inspact 8e76cf05adee ```                    
 - Display detailed information on one or more containers

``` docker container logs 8e76cf05adee  ```                        
- Fetch the logs of a container
      
## Docker port mapping, rename container, restart container, exec container
 
 ``` docker container run -d -p 80:8081 --name test_nginx nginx ```       
 - run nginx container with port 80 to 8081 name  test_nginx
 
 ``` docker container exec -it 8e76cf05adee /bin/bash  ```               
 - login in existinf running instance 
 
 ``` docker container rename  8e76cf05adee newtest_nginx ```      
 - rename your existing container name test_nginx to newtest_nginx
 
 ``` docker container restart  8e76cf05adee ```                   
 - restart existing container
 
## Attach to running container, kill, wait, pause, unpause, prune, port
  
``` docker container attach 8e76cf05adee ```             
- attach container inside the env.
  
``` docker container kill 8e76cf05adee ```                
- kill container immediately
 
``` docker container wait 8e76cf05adee ```                
- waiting  to container stop 
 
``` docker container stop 8e76cf05adee ```                 
- stop container wiith soft way
 
``` docker container pause 8e76cf05adee ```                 
- Pause resources for your container
 
``` docker container unpause 8e76cf05adee ```                
- unpause your container resources 
 
``` docker container prune ```                              
- delete your all stopped containers
 
``` docker container port container_id/container_name ```
- check the bind port with your host machine 
 
 ## create docker container, diff docker container and copy file into container
 
``` docker container diff 8e76cf05adee ```         
- It'll show you modify files system inside container files/directories (A : add, C: create, D: delete)

``` watch 'docker container diff 8e76cf05adee'  ```

- watch modify files in container 

``` docker container cp /tmp/abc.txt 8e76cf05adee:/tmp/  ```
- copy file into container

``` docker container attach 8e76cf05adee ```          
- check copied files inside container

## Export/Import docker container

``` docker container export 8e76cf05adee > export_image.tar  ```   
- Export your container images with .tar file

``` docker container export 8e76cf05adee -o  export_image.tar ```  
- Export your container images with .tar file

``` docker image import export_image.tar new_export_image ```       
- import .tar file imange as conatiner 

``` docker image  ls | grep new_export_image ```

``` docker container run  -it new_export_image /bin/bash  ```      
- to run the new_export_image 


## how to create docker image from running container (docker commit)

``` docker container commit --author "Ajay Rajput" -m "this is new test image" 8e76cf05adee new_commit_images  ```
- commit image

``` docker container run -it new_commit_images /bin/bash ```


## How to push image on docker hub, image tag, image pull, docker login

``` docker pull ubuntu:18.04   ```          
- pull image from docker hub with sepific tag like :18.04

``` docker image tag ubuntu:18.04 onkardevops/ubuntu:18.04_latest ``` 
- tag exist ubuntu:18.04 image

``` docker login ```

``` docker push onkardevops/ubuntu:18.04_latest  ```
- push on github account

``` docker pull onkardevops/ubuntu:18.04_latest ```         
- download in your system


## How to inspect remove,inspect, list and history for the docker image
``` docker images ls --format '{{.ID}} , {{.Repository}}  -{{.Tag}}'  ```  
- view image by id, repo & Tag

``` docker image history ubuntu ```                                         
- check history on ubuntu image

``` docker image rm ubuntu:18.04 ```

``` docker image inspect ubuntu | less ```

``` docker image prune ```


## Docker save / docker load. Diff between export and save & load &import

``` docker image save kibana > kibana.tar ``` 
- save as standred  output (save all parent layer, save tag, name version, repos, all info etc..)

``` docker image load  < kibana.tar ```        
- ipmort as standrrad input (import all parent layer, save tag, name version, repos, all info etc..)) 

``` docker image import  ```   
- single layer without all parent layer, save tag, name version, repos, all info etc..

``` docker container expot ```
- save only one layer without taking volume backup 

## How to create Dockerfile 

Writing your first Dockerfile
To build a Docker image, you need to create a Dockerfile. It is a plain text file with instructions and arguments.  

- FROM — set base image

- RUN — execute command in container

- ENV — set environment variable

- WORKDIR — set working directory

- VOLUME — create mount-point for a volume

- CMD — set executable for container
 
## Dockerfile (add, copy, user) difference between copy and add in docker file


    FROM ubuntu:18.04
    LABEL name : "Ajay Rajput"
    LABEL email : "onkar.devops@gmail.com"

    ENV NAME ajay
    ENV PASS password@123

    RUN pwd >/tmp/test.txt
    RUN cd /tmp
    RUN pwd >/tmp/test1.txt

    #Define working directory
    WORKDIR /tmp                   

    RUN pwd >/tmp/test2.txt

    RUN apt-get update && apt-get install -y apache2 && apt-get clean && rm -rf /var/lib/apt/lists/*

    RUN useradd -d /home/ajay -g root -G sudo -m -p $(echo "$pass" | openssl passwd -l -stdin) $NAME  
    
    #add uesr and switch user
    RUN whoiam > /tmp/test3.txt

    #switch in ajay user
    USER $NAME                                               

    RUN whoami  > /tmp/test3.txt

    #COPY only copy test.tar file to /tmp
    COPY test.tar /tmp                          

    #ADD extract test1.tar to  /tmp 
    ADD test1.tar /tmp                          

    #Define default command
    CMD ["/bin/bash"]   




## Dockerfile ( Expose and create a SSH container using dockerfile )

    FROM ubuntu:18.04

    LABEL name : "Ajay Rajput"

    LABEL email : "onkar.devops@gmail.com"

    ENV NAME ajay 

    ENV PASS password@123

    RUN apt-get update && apt-get install -y openssh-server 
    
    #add uesr and switch user
    RUN useradd -d /home/ajay -g root -G sudo -m -p $(echo "$pass" | openssl passwd -l -stdin) $NAME  

    #open ports
    EXPOSE 22 443 80                   
 
    #Define default command. 
    CMD ["/usr/sbin/sshd",  "-D"]                         

``` docker image build -t ubuntu:18.04_latest . ```

``` docker images ls ```

``` docker container run -itd ubuntu:18.04_latest ```

``` docker container ls ```

## Dockerfile (Entrypoint)

    FROM ubuntu:18.04

    LABEL name : "Ajay Rajput"

    LABEL email : "onkar.devops@gmail.com"

    ENV NAME ajay 

    ENV PASS password@123

    RUN mkdir -p /var/run/sshd

    RUN apt-get update && apt-get install -y python tee

    COPY test.sh /tmp/

    ENTRYPOINT ["/tmp/tesh.sh"]


## Docker Volume ( Docker Storage), mysql data persist in docker container

``` docker container run --name mysql_instance -e MYSQL_ALLOW_EMPTY_PASSWORD=true mysql ```

``` docker volume ls ```

``` docker volume create abc ```

``` docker volume inspect abc ```

``` docker container run -itd -v abc:/var/lib/mysql --name mysql_instance -e MYSQL_ALLOW_EMPTY_PASSWORD=true mysql ```

## Docker Volume (Remove, Prune)

``` docker volume rm abc ```

``` docker volume prune ```   
- remove unused volumes 

## Docker Bind mount

``` docker container run -itd -v /home/ajay/bind/test:/temp/test --name mysql_instance  mysql ```

``` docker container run -itd -v $(pwd):/temp/test --name mysql_instance  mysql ```
- bind your current working directory folder 

 or 

``` docker container run -it --mount type=bind,source= $(pwd),target=/temp/test  mysql bash ```


## Docker Networking (Bridge Network Overview)

``` docker network ls ```

 ``` docker network inspect bridge ```

 ``` docker network create test ```

``` docker container run -it --network=test ubuntu:18.04 bash ```

##  Docker Networking (DNS Enable)

``` docker container run -it --network=test ubuntu:18.04 bash ```

``` hostname  ```

``` docker container run -it --network=test ubuntu:18.04 bash ```

``` ping hostname ```    

- By default domain name come with custome network already enable with custome network.

## Docker Networking (Host Network)

``` docker network ls ```

` f2efa836f00e        host                host                local `

``` docker container run -it --network=host ubuntu:18.04 bash ```

``` ifconfig  ``` 

- it will show you base host ip address range 192.168.21.1 

``` docker container run -itd --network=host nginx ``` 

- http://hostip to check nginx, this type of networking all processes and pid's seprated with host network. you cann't create multipal host network.

## Docker Networking (Null Network, None Network)
this network not use for any other network. if you not mention any network name it will assign to bridge network.

``` docker container run -it --network=null ubuntu:18.04 bash ```

``` ifconfig  ```
- it would be loop back  ip address : 127.0.0.1

##  Docker Networking (Connect, Disconnect)
how to assign a multipul network name to a single container?

``` docker container run -it network bridge ubuntu:18.04 bash ```

``` ifconfig ``` 
- it will show you one eth0

``` docker network  connect  test 2683sjbjsdw  ```
- it will add eth1 in '2683sjbjsdw' container

``` docker network  connect  test 2683sjbjsdw ```  
- it will remove eth1 in '2683sjbjsdw' container

## Docker Networking (Remove, Proun)

``` docker network rm test ``` 
- remove test network

``` docker network prune ```
- remove unused network

## Docker Registry/Repository (Insecure)

``` docker container run -d -p 5000:5000 --name local_registry registry ```        
- volume will create automatic.

``` docker container ls   ``` 

``` http://127.0.0.1:5000/v2/_catlog ```       
- to check the images under registry

``` docker container inspect registry ```     
- Driver : local => you can point it to s3, google cloud drive etc ...

``` docker image tag nginx 127.0.0.1:5000/nginx:test ```

``` docker image push  127.0.0.1:5000/nginx:test ```  
- http://127.0.0.1:5000/v2/_catlog 

``` docker image rm nginx:test ```

``` docker image pull 127.0.0.1:5000/nginx:test ```     
- docker allow https to 127.0.0.1/8 network, docker bydefault allow secure network

``` docker image tag mysql 10.0.2.11:5000/mysql ```

``` docker image push 10.0.2.11:5000/mysql   ```    
- it'll not allow to push throwing https client error insecure. So you have to create file : daemon.json file

``` vim daemon.json ```

{
  "insecure-registries" : ["10.0.2.11:5000"]      
}

``` sudo mv daemon.json /etc/docker/ ```

``` sudo service docker restart ```

``` docker container start ```

``` docker push 10.0.2.11:5000/mysql ```

``` docker pull 10.0.2.11:5000/mysql ```

## Docker Registry/Repository (secure)

``` mkdir certs ```

``` openssl req  -newkey rsa:2048 -nodes -sha256 -keyout certs/domain.key -x509 -days 365 -out certs/domain.crt ```

```` hotname : repo.docker.local ```

``` cd certs/ ```

``` ls ```

``` cd /etc/docker/ ```

``` mkdir certs.d ``` 

``` cd certs.d  ```

``` mkdir repo.docker.local:5000 ```

``` cp certs/domain.crt /etc/docker/certs.d/repo.docker.local:5000/ca.crt ```

``` sudo service docker restart ```

``` docker container run -d  -p 5000:5000 --name secure_registry -v $(pwd)/certs/:/certs -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/domain.crt -e REGISTRY_HTTP_TLS_KEY=/certs/domain.key registry ```

``` docker image tag apache repo.docker.local:5000/apache  ```

``` vim /etc/hosts ```

10.0.0.2 repo.docker.local

``` docker image push repo.docker.local:5000/apache ```


## Docker Registry With Basic Authentication

``` mkdir auth ```

`
docker container run --entrypoint htpasswd  registry -bnB  ajay p
assword >auth/htpasswd  
`

- b means run in batch mode, n means show output in display and B means bcrypt ```

``` cat auth/htpasswd ```

``` docker container run -d -p 5000:5000 --name registry -v "$(pwd)"/auth:/auth -v "$(pwd)"/certs:/certs -e "REGISTRY_AUTH=htpasswd" -e  "REGISTRY_AUTH_HTPASSWD_REALM=Registry Realm" -e "REGISTRY_AUTH_HTPASSWD_PATH=/auth/htpasswd" -e "REGISTRY_HTTP_TLS_CERTIFICATE=/certs/domain.crt -e REGISTRY_HTTP_TLS_KEY=/certs/domain.key registry ```


``` docker image ls ```

``` docker login repo.docker.local:5000  ``` 
- like docker login command

``` user : ajay ```
``` password : password ```


``` docker image push repo.docker.local:5000/apache ```

``` docker logout repo.docker.local:5000 ```  
- logout user 



# Docker Compose

``` docker-compose.yml ```

    #version means which docker engin supports 
    version: '3'          
    services:             
          webapp1:
         image: nginx
         ports:
         - "8080:80"

      webapp2:
         image: nginx
         ports:
         - "8001:81"


``` docker-compose up -d    ```    

``` docker-compose.yml ```

    version: '3'           
    services:             
      webapp1:
         image: nginx
         ports:
         - "8083:80"           

      webapp2:
         image: nginx
         ports:
         - "8001:81"

- modify port only in existing container     

``` docker-compose up -d ``` 
- it'll recreate only 8083 nginx container not for 8001. 

``` docker-compose -f docker-compose2.yml up -d ```
- new file with -f 

``` docker-compose -f docker-compose2.yml down ```

``` docker-compose -f docker-compose2.json up -d  ```
- yml super set of json file

## Docker Compose  Basic Command ( create, start, stop, rm, up, down)

``` docker-compose create ```
- it will create container but not in run staus , also it will not create any network by default.

``` docker-compose up --no-start  ```
- create a network

``` docker-compose start ```
- start container as created mode

``` docker-compose stop ```
- started container to stop 

``` docker-compose rm ```
- container remove, network not remome

``` docker-compose down ```
- remove container but not remove volume

## Docker Compose ( ps, pause, unpause )

``` docker-compose ps ```
- list docker containers state

``` docker-compose pause  ```
-  running container to pause

``` docker-compose unpause  ```
- paused container to unpaused state up

## Docker Compose Kill, exec, run, help, log

``` docker-compose kill ```
- kill running container

``` docker-compose start ``` 
- start killed container

``` docker-compose port webapp1 80 ```
- list container map ip with host port

``` docker-compose logs -f   ```
- see container logs 

``` docker-compose exec webapp1 ls ```
- run ls command inside webapp1 container

``` docker-compose run webapp1 ls ```
- it wiil create new container to list ls output after complete ls output new created container will remove.

``` docker-compose restart ```
- restart existing running containers

``` docker-compose pull ```   
- download images from docker hub

## Docker Compose  Scale, Top


``` docker-compose.yml ```

    version: '3'           
    services:             
      webapp1:
         image: nginx

      webapp2:
         image: nginx

``` docker-compose scale webapp1=2 webapp2=6 ```

``` docker-compose ps ```

``` docker-compose top ```

## Docker Compose : Bind Mount

``` vim docker-compose.yml ```

    version: '3'           
    services:             
      webapp1:
         image: nginx
         ports:
           - "8000:80"
         volume:
           - ./data/:/usr/share/nginx/html/  


``` docker-compose up -d ```

## Docker Compose use build, image, dockerfile in Docker-Compose.yml

``` vim Dockerfile ```

    FROM python:3.7-alpine
    WORKDIR /code
    ENV FLASK_APP app.py
    ENV FLASK_RUN_HOST 0.0.0.0
    RUN apk add --no-cache gcc musl-dev linux-headers
    COPY requirements.txt requirements.txt
    RUN pip install -r requirements.txt
    COPY . .
    CMD ["flask", "run"]

``` docker-compose.yml ```

    version: '3'
    services:
      web:
        build: .
        ports:
          - "5000:5000"
          image: 'onkardevops/mypythonimage:latest'
      redis:
        image: "redis:alpine"

``` docker-compose up ```

``` docker-compose exec web env ```



































 



































 
 



