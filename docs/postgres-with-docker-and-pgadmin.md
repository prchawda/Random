**Getting started with Postgres with Docker and PgAdmin**

In this article, we will learn how to get started using PostgreSQL on Docker. PostgreSQL is one of the most popular open-source databases that is being used by a lot of developers. It is highly stable and has a large community that maintains and supports the database development lifecycle. PostgreSQL can be installed on any operating system like Windows, macOS, and Linux. With the rise in containerization technologies, PostgreSQL can now also be installed using Docker.

Docker is a popular application that enables users to bundle applications into containers that help in the easy deployment of resources. This article will introduce you to the basic concepts of installing and using Docker on a Mac and then pulling a PostgreSQL Docker image from the Docker Hub repository. In addition to PostgreSQL, we will also install PgAmin4 using Docker, which can be used to administer the PostgreSQL instance with Docker.

**What is Docker and why do we need it?**

Docker is an open-source containerized application technology using which you can deploy your applications easily on-premises or on the cloud. You can bundle your applications into docker images that will already store the necessary configurations required to run the application. During deployment, you need to just run the docker image on the production environment, and all the configurations will be in from the image.

**Installing the PostgreSQL Docker image**

Now, we are good to start pulling the PostgreSQL Docker image to our local machine. Docker images are hosted on a repository on the cloud known as the Docker hub. You can pull images from the Docker hub and can also publish your images to the Docker hub. The official PostgreSQL Docker image can be found on the [Docker Hub](https://hub.docker.com/_/postgres). To pull the image to your local, you can run the following command on the terminal.

docker pull Postgres

**FIG3**

Once the docker image has been pulled to the local machine, you can check all installed images on your local by the following command.

docker image ls

**FIG4**

As you can see in the above figure, the PostgreSQL image has been installed on your machine. Now, that the image is installed, the next step would be to run this image. When we run a Docker image, a new container is created for that image. In other words, a container is a running instance of a Docker imag

**Running the PostgreSQL image**

To run the docker image for PostgreSQL, you can use the following command.

docker run --name pgsql-dev -e POSTGRES_PASSWORD=Welcome4$ -p 5432:5432 Postgres

In the above command, we call the run method on docker that will create a running instance of an existing docker image. Along with that we also need to pass a few parameters as follows.

-   Name -- We need to provide a name for the container that is going to be created when docker will run the image.
-   Environment variables -- When running the docker image for PostgreSQL, the only mandatory environment variable that we need to set is the password for the database. The default user is 'Postgres'.
-   Port -- We need to define on which port is the database going to run in the container and which port on the host can be used to access it. As you can see, there are two ports mentioned in the command which are in the form HOST_PORT:DOCKER_PORT.

Finally, we provide the name of the docker image from which the container is to be created.

FIG5

Once you run the command, you can see that the container starts up and is ready to accept database connections. An important point to note here is that we have not executed the command in detached mode, which means the database or the container will be running as long as the terminal is open.

Now to connect to the database, let us open a new terminal window and type the following command.

docker exec -it pgsql-dev bash

This command will start an interactive terminal inside the container. Next, you can start the PostgreSQL by running the following command on the same terminal.

psql -h localhost _U postgres

FIG6

You can list all the running databases and execute other SQL statements once connected through the command line utility. If you prefer using the PostgreSQL environment through a GUI, then follow along. We will also install the PgAdmin4 utility and connect it to the PostgreSQL database from it.

**Installing the PgAdmin4 docker image**

You can follow similar steps while pulling the docker image for PgAdmin4. You can run the following command on a new terminal window.

docker pull dpage/pgadmin4

**FIG7**

To run the PgAdmin4 docker image, you need to run the following command.

docker run -e 'PGADMIN_DEFAULT_EMAIL=test@domain.local' -e 'PGADMIN_DEFAULT_PASSWORD=test1234' -p 8080:80 --name pgadmin4-dev dpage/pgadmin4

The PgAdmin4 image accepts two mandatory environment variables PGADMIN_DEFAULT_EMAIL and PGADMIN_DEFAULT_PASSWORD that enable the web application to log in. Along with that, we also specify the port on which the PgAdmin4 will run. We have specified that port 80 of docker shall bind to port 8080 of the host machine. That means the web app will be available on port 8080 from the host machine.

**FIG8**

Once the docker container is up and running, you can head over to the web browser and connect to [http://localhost:8080](http://localhost:8080/).

FIG9

**Connecting to PostgreSQL database from PgAdmin4**

Once you have logged in to the PgAdmin4 console, the final step is to create a server by connecting to the PostgreSQL docker instance that is already running on the machine. For that, we need to know the exact IP address of the PostgreSQL database. We can fetch that information by inspecting the container that is running. Open a new terminal window and type the following command.

docker inspect pgsql-dev

**Fig10**

Scroll down below until you find the section Networks. bridge.IPAddress. Copy the IPAddress and store it for later use.

FIG11

Now that we have the IP address of the PostgreSQL database, head over to the PgAdmin4 window and create a new server. Use the IP Address as copied in the previous step and the password you used while creating the container. Click on **Save**.

Fig12

Once the connection is successful, you will see the dashboard for the instance will open up with the default Postgres database.

FIG13
