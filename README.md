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
