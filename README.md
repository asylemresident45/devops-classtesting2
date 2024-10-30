Pre-lab Requirements

go to https://tak.gov

    Create an Account 

at the top of the screen go to Products>other>tak server



Lab: Creating a TAC Container

Objective

This lab will guide you through the process of creating a TAC container using Docker. You will learn to set up a docker container running tak.

    Docker installed on your machine
    The takserver-docker-5.2-RELEASE-43.zip file downloaded and accessible

Step 1: setting up

    Start by making a new directory using the mkdir command 

    place the takserver-docker-5.2-Release-43.zip file inside the directory you just created

Step 2: Unzip the Downloaded File

    Next, run the following command to extract the contents of the ZIP file:

    unzip takserver-docker-5.2-RELEASE-43.zip 

    This command unzips the takserver-docker-5.2-RELEASE-43.zip file, allowing you to access the necessary files for building and running the TAC container. The extracted contents include Dockerfiles and configurations needed for the next steps.

Step 3: Navigate to the Unzipped Directory

    Now, change your current working directory to the one just extracted:

    cd takserver-docker-5.2-RELEASE-43/

    This command ensures you are in the correct directory to access the Dockerfiles and other resources required for the subsequent commands.

Step 4: Build the Database Docker Image

    Next, you will build the database Docker image by executing the following command:

    docker build -t takserver-db:"$(cat tak/version.txt)" -f docker/Dockerfile.takserver-db .

    This command builds a Docker image named takserver-db and tags it with the version specified in version.txt. The -f option indicates the specific Dockerfile to use. Building this image is essential for setting up the database component of your TAC server, using the configurations defined in the Dockerfile.

Step 5: Create a Docker Network

    Now, create a Docker network for your containers by running:

    docker network create takserver-"$(cat tak/version.txt)"

    This command establishes a new Docker network named takserver-<version>. A dedicated network allows the containers to communicate securely while isolating them from others, which is important for maintaining the integrity of your application.

Step 6: Run the Database Container

    Next, start the database container with the following command:

    docker run -d -v $(pwd)/tak:/opt/tak:z -p 5432:5432 --network takserver-"$(cat tak/version.txt)" --name takserver-db takserver-db:"$(cat tak/version.txt)"

    This command runs the takserver-db image in detached mode. The -v option mounts the tak directory to /opt/tak in the container for data persistence. The -p option maps port 5432 on the host to port 5432 in the container, enabling access to the database. This step is crucial as it starts the database service required for the TAC application, ensuring it can manage and store data effectively.
Step 7: Build the TAC Server Docker Image

    Now, proceed to build the TAC server Docker image by executing

    docker build -t takserver:"$(cat tak/version.txt)" -f docker/Dockerfile.takserver .

    This command builds another Docker image named takserver using the Dockerfile.takserver. This image contains the main application for TAC, which will interact with the database container created earlier.
Step 8: Run the TAC Server Container

    Next, run the TAC server container with the following command:

    docker run -d -v $(pwd)/tak:/opt/tak:z -p 8089:8089 -p 8443:8443 -p 8444:8444 -p 9000:9000 --network takserver-"$(cat tak/version.txt)" --name takserver takserver:"$(cat tak/version.txt)"

    This command starts the takserver image in detached mode, with multiple ports (8089, 8443, 8444, 9000) mapped from the host to the container. This setup allows external access to different services provided by the TAC application, which is essential for its functionality.
Step 9: List Running Containers

    To verify that your containers are running correctly, use the following command:

    docker ps

    This command lists all currently active Docker containers, allowing you to check the status of your TAC server and database.
step 10: Stop a Running Container

    If you need to stop a running container, you can do so with:

docker stop <container_name_or_id>

    Replace <container_name_or_id> with the actual name or ID of the container. This command stops the specified container, which is necessary when you need to make changes, troubleshoot, or gracefully shut down services.
    Step 11: Remove a Stopped Container

Finally, to clean up your environment, you can remove a stopped container by running:

    docker rm <container_name_or_id>

    Again, replace <container_name_or_id> with the appropriate name or ID. This command deletes the specified container, helping you manage system resources and avoid clutter in your Docker environment.
    
Conclusion

    By following these steps, you have successfully created a TAC container using Docker. You’ve learned how to structure your project, build Docker images, and run containers effectively. This knowledge is essential for managing applications in containerized environments.
