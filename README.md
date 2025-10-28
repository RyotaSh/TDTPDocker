### 1-1 For which reason is it better to run the container with a flag -e to give the environment variables rather than put them directly in the Dockerfile?

Everyone can see the dockerfile so putting sensitive information like your database logins is not safe , so it's better to use flag -e to keeps secrets out of the dockerfile , the container will only gets those variable at runtime .


### dockerfile
ENV POSTGRES_DB=db \
   POSTGRES_USER=usr \
   POSTGRES_PASSWORD=pwd


### 1-2 Why do we need a volume to be attached to our postgres container?
We need a volume attached to our PostgreSQL container because when a container is destroyed, all data written is lost ,PostgreSQL stores its data in /var/lib/postgresql/data inside the container 
Without a volume, if the container stops or is removed, all database data would be permanently lost.
The volume persists the database data on the host's disk ensuring that data survives container restarts/deletion.
### 1-3 Document your database container essentials: commands and Dockerfile.
## Prerequisites
- Docker installed on your system
# 1. Create Docker Network
docker network create app-network
# 2.Build the Database Image
docker build -t postgres-db:latest .
# 3. Run the Database Container
docker run -d --name postgres-container --network app-network -e POSTGRES_DB=db -e POSTGRES_USER=usr -e POSTGRES_PASSWORD=pwd -v postgres_data:/var/lib/postgresql/data -p 5432:5432 postgres-db:latest


# 5.ESSENTIAL COMMANDS:
Build: docker build -t 
Run: -docker run -d --name ... -v db_data:/var/lib/postgresql/data -p ....:.... ....
     -docker run -d --name containername --network networkname -p 8090:8080 imagesname
Stop container: docker stop containername
delete container: docker rm containername
# Check running containers
docker ps
# Start containers
docker start container1 container2..

### 1-4 Why do we need a multistage build? And explain each step of this dockerfile.
We need multistage builds to separate the build environment from the runtime environment, resulting in smaller and more secure production images.
## step of this dockerfile
# Build stage
From ... : Creates build environment with JDK
ENV MYAPP_HOME=/opt/myapp - Sets environment variable for app directory
WORKDIR $MYAPP_HOME - Sets working directory to /opt/myapp
RUN apk add --no-cache maven - Installs Maven
COPY pom.xml . & COPY src ./src - Copies source code
RUN mvn package -DskipTests - Builds the JAR
# Run Stage
FROM eclipse-temurin:21-jre-alpine - JRE only for running
ENV MYAPP_HOME=/opt/myapp & WORKDIR $MYAPP_HOME - Same directory structure
COPY --from=myapp-build $MYAPP_HOME/target/*.jar $MYAPP_HOME/myapp.jar - Copies only the JAR
ENTRYPOINT ["java", "-jar", "myapp.jar"] - Runs the app


### Quick Start this part
cd backend/simpleapi
docker build -t spring-backend:latest .
docker run -d --name spring-backend --network app-network -p 8080:8080 spring-backend:latest
## Test the API
curl "http://localhost:8080/?name=Alice"


### HTTP STAGE 
build : docker build -t my-apache . in TP1/HTTP
then run : docker run -d --name apache-web --network app-network -p 80:80 my-apache


Test the API
curl "http://localhost:8080/?name=Alice"

HTTP STAGE
build : docker build -t my-apache . in TP1/HTTP then run : docker run -d --name apache-web --network app-network -p 80:80 my-apache

### 1-5 Why do we need a reverse proxy?
A reverse proxy sits between users and your server to forward requests , improving security.

### 1-6 Why is Docker Compose important?
Docker Compose simplifies managing multi-container applications. Instead of manually starting, stopping, or linking containers, you define all services in a single YAML file. Then you can start everything with one command (docker compose up) and manage dependencies, networks, and volumes consistently.

### 1-7 Docker Compose most important commands:
docker compose up -d Build and start all services in detached mode. docker compose down Stop and remove containers, networks, and volumes created by up docker compose ps

### 1-8 Document your docker-compose.yml file:
Services:

backend: Spring Boot application, depends on database. Configured with environment variables for DB connection. database: PostgreSQL, persistent storage via named volume postgres-data. Not exposed to host httpd: Apache reverse proxy, depends on backend. Exposes port 80 to the host Volumes:

postgres-data persists database data across container restarts.

### 1-9 Document your publication commands and published images in dockerhub.
give a name to them : docker tag my-postgres-db chengboss/my-postgres-db:1.0

docker tag spring-backend chengboss/spring-backend:1.0 docker tag my-apache chengboss/my-apache:1.0 then push them docker push chengboss/my-postgres-db:1.0 docker push chengboss/spring-backend:1.0 docker push chengboss/my-apache:1.0

### 1-10 Why do we put our images into an online repo?
We put our images into an online repository (like Docker Hub) so other team members or machines can pull and run the same images easily, ensuring consistency and easy deployment.



###### RELAUNCH EVERYTHING ON A NEW PC :
# Créer le réseau Docker:
docker network create app-network

# Construire l’image Postgres : Depuis C:\Users\Cheng\Documents\GitHub\TDTPDocker\TP1 :
docker build -t my-postgres .

# Lancer le conteneur Postgres sur app-network avec le bon nom:
docker run -d `
  --name postgres-db `
  --network app-network `
  -e POSTGRES_DB=db `
  -e POSTGRES_USER=usr `
  -e POSTGRES_PASSWORD=pwd `
  -p 5432:5432 `
  my-postgres

# Construire l’image Spring Boot:
docker build -t spring-backend .

# Lancer Spring Boot sur app-network
docker run -d `
  --name spring-backend `
  --network app-network `
  -p 8080:8080 `
  spring-backend

#   Construire l’image HTTP (Apache + Proxy) 
  Depuis C:\Users\Cheng\Documents\GitHub\TDTPDocker\TP1\HTTP :
  docker build -t my-http .

#   Lancer le conteneur HTTP sur app-network
  docker run -d `
  --name http-frontend `
  --network app-network `
  -p 80:80 `
  my-http

### C:\Users\Cheng\Documents\GitHub\TDTPDocker\TP1\backend\simpleapi\src\main\resources\application.yml remettre ca apres 28/10/2025
  spring:
  jpa:
    properties:
      hibernate:
        jdbc:
          lob:
            non_contextual_creation: true
    generate-ddl: false
    open-in-view: true

  datasource:
    url: jdbc:postgresql://postgres-db:5432/db
    username: usr
    password: pwd
    driver-class-name: org.postgresql.Driver

management:
  server:
    add-application-context-header: false
  endpoints:
    web:
      exposure:
        include: health,info,env,metrics,beans,configprops




### 3-1 Document your inventory and base commands
all: top-level group for all hosts.

vars: variables applied to all hosts.

ansible_user: SSH user.

ansible_ssh_private_key_file: path to your private key.

children: sub-groups.

prod: production group with your server hostname.
## base command
ansible all -i inventories/setup.yml -m ping

ansible all -i inventories/setup.yml -m setup ####gather facts get system info

ansible all -i inventories/setup.yml -m setup -a "filter=ansible_distribution*" ###filter specific fact

ansible all -i inventories/setup.yml -m apt -a "name=package_name state=present" --become install package

ansible all -i inventories/setup.yml -m apt -a "name=apache2 state=absent" --become remove package

ansible-playbook -i inventories/setup.yml playbook.yml
## step for setup
# Install WSL (Windows Subsystem for Linux) and Ansible there
wsl --install -d Ubuntu

Then inside Ubuntu:
sudo apt update
sudo apt install ansible -y 

then test with : 
ansible all -i /mnt/c/Users/Cheng/Documents/GitHub/TDTPDocker/ansible/inventories/setup.yml -m ping
# Copy the key into your WSL home
cp /mnt/c/Users/Cheng/Documents/GitHub/TDTPDocker/ansible/inventories/id_rsa ~/.ssh/id_rsa
# Set proper permissions
chmod 600 ~/.ssh/id_rsa

Then update your setup.yml inventory to point to the new path:
ansible_ssh_private_key_file: ~/.ssh/id_rsa

ansible all -i setup.yml -m ping should work again.


### Docker installed on remote server
To check execute this in ubuntu after you installed it :
ansible all -i inventories/setup.yml -m command -a "docker --version"
### 3-2 Document your playbook
my playbook is light and short because i've put everything inside main.yml in docker and i execute those taks using docker role
### what a docker_container task should look like:
- name: Run HTTPD
  docker_container:
    name: httpd
    image: your image name from DockerHub.
### 3-3 Document your docker_container tasks configuration.

### Is it really safe to deploy automatically every new image on the hub ? explain. What can I do to make it more secure?
It is not really safe to deploy automatically every new image from Docker Hub, because images could contain bugs, vulnerabilities, or even malicious code. Automatic deployment bypasses testing and validation, which increases the risk of breaking your application or exposing it to security issues. To make it more secure, you should deploy only trusted, tested images, use fixed tags instead of latest, and implement a staging environment or manual approval step before deploying to production..


###
