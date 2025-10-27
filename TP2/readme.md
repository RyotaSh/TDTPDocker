### Documentation
### 2-1 What are testcontainers?
Testcontainers is an open source framework for providing throwaway, lightweight instances of databases, message brokers, web browsers, or just about anything that can run in a Docker container.
### 2-2 For what purpose do we need to use secured variables ?
We need to use secured variables to protect sensitive information like usernames, passwords, or API token thus keeping credentials hidden from anyone browsing the repository.
### 2-3 Why did we put needs: build-and-test-backend on this job? Maybe try without this and you will see!
We put needs: test-backend so the Docker image job runs only after tests pass. Without it, the job would start immediately, even if tests fail. It ensures correct order and prevents broken images.
### 2-4 For what purpose do we need to push docker images?
We need to push Docker images so they’re stored and accessible on Docker Hub, allowing others to pull and run the exact same version of the application easily.
###
###
###
###
###
###