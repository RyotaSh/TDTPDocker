1-1 For which reason is it better to run the container with a flag -e to give the environment variables rather than put them directly in the Dockerfile?

Everyone can see the dockerfile so putting sensitive information like your database logins is not safe , so it's better to use flag -e to keeps secrets out of the dockerfile , the container will only gets those variable at runtime .


### dockerfile
ENV POSTGRES_DB=db \
   POSTGRES_USER=usr \
   POSTGRES_PASSWORD=pwd


### 1-2 Why do we need a volume to be attached to our postgres container?

### 1-3 Document your database container essentials: commands and Dockerfile.