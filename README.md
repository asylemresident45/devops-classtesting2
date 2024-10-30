Lab: Creating a TAC Container

Objective

This lab will guide you through the process of creating a TAC container using Docker. You will learn to set up a docker container running tak.

    Docker installed on your machine
    The takserver-docker-5.2-RELEASE-43.zip file downloaded and accessible

Step 1: setting up

A. Start by making a new directory using the mkdir command 

B. place the takserver-docker-5.2-Release-43.zip file inside the directory you just created

Step 2: Unzip the Downloaded File

Command:

unzip takserver-docker-5.2-RELEASE-43.zip 

Explanation:

    This command extracts the contents of the takserver-docker-5.2-RELEASE-43.zip file into the current directory.
    Why: The ZIP file contains the necessary files, including Dockerfiles and configuration, that are required to build and run your TAC container.

Step 3: Navigate to the Unzipped Directory

Command:

cd takserver-docker-5.2-RELEASE-43/

Explanation:

    This command changes your current working directory to the one just extracted.
    Why: You need to be in the correct directory to access the Dockerfiles and other resources needed for the subsequent steps.

Step 4: Build the Database Docker Image

Command:

docker build -t takserver-db:"$(cat tak/version.txt)" -f docker/Dockerfile.takserver-db .

Explanation:

    This command builds a Docker image named takserver-db with a tag defined in the version.txt file.
    The -f option specifies the Dockerfile to use.
    Why: Building this image is essential for creating the database component of your TAC server, using the specified configurations from the Dockerfile.

Step 5: Create a Docker Network

Command:

docker network create takserver-"$(cat tak/version.txt)"

Explanation:

    This command creates a new Docker network named takserver-<version>.
    Why: A dedicated network allows the containers to communicate securely with each other while isolating them from other containers, which is important for maintaining application integrity.

Step 6: Run the Database Container

Command:

docker run -d -v $(pwd)/tak:/opt/tak:z -p 5432:5432 --network takserver-"$(cat tak/version.txt)" --name takserver-db takserver-db:"$(cat tak/version.txt)"

Explanation:

    This command runs the previously built takserver-db image in a detached mode (-d).
    The -v option mounts the tak directory to /opt/tak in the container, allowing data persistence.
    The -p option maps port 5432 on the host to port 5432 in the container, which is necessary for database access.
    Why: This step starts the database service required for the TAC application, ensuring it can store and manage data persistently.

Step 7: Build the TAC Server Docker Image

Command:

docker build -t takserver:"$(cat tak/version.txt)" -f docker/Dockerfile.takserver .

Explanation:

    This command builds another Docker image named takserver using the Dockerfile.takserver.
    Why: This image contains the main application for TAC, which will communicate with the database container.

Step 8: Run the TAC Server Container

Command:

docker run -d -v $(pwd)/tak:/opt/tak:z -p 8089:8089 -p 8443:8443 -p 8444:8444 -p 9000:9000 --network takserver-"$(cat tak/version.txt)" --name takserver takserver:"$(cat tak/version.txt)"

Explanation:

    This command runs the takserver image in detached mode.
    Multiple -p options map various ports (8089, 8443, 8444, 9000) from the host to the container, allowing access to different services.
    Why: Running the TAC application is essential for providing the functionalities required by users, and the port mappings enable external access to those services.

Step 9: List Running Containers

Command:

docker ps

Explanation:

    This command lists all currently running Docker containers.
    Why: It's useful for verifying that your containers are running correctly and for checking their status.

Step 10: Stop a Running Container

Command:

docker stop <container_name_or_id>

Explanation:

    This command stops a running container by its name or ID.
    Why: Stopping containers is a standard procedure when you need to make changes, troubleshoot, or shut down services gracefully.

Step 11: Remove a Stopped Container

Command:

docker rm <container_name_or_id>

Explanation:

    This command removes a stopped container from your system.
    Why: Cleaning up stopped containers helps manage system resources and avoid clutter, making it easier to maintain your Docker environment.

Conclusion

By following these steps, you have successfully created a TAC container using Docker.
