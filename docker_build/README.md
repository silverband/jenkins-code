#Custom Jenkins Docker Container
This is the DockerFile, with required files that is used to build our custom Jenkins System
Please review the files found in the config sub directory, as there is, at the very least,
docker secrets set for the jenkins admin user and it's password.
You will want to confirm the docker group id in the Dockerfile before building with your
host system docker group, as this will allow jenkins to execute docker containers in pipelines
