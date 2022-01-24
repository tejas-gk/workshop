# About the App
It's a basic PHP - MySQL application which has a form which takes First Name, Last Name and Phone Number as inputs and upon clicking "Submit" button, the data is inserted into the MySQL Database.

The data in the MySQL table will be displayed below the form.


# How to dockerize this PHP Application?

1.  Create a file with the name `Dockerfile` in the root of the project.
2.  Contents of Dockerfile:
    <br>
    a. Get the base image: `FROM php:7.4-apache`. This pulls the base php 7.4 image with inbuild apache server.
    <br>
    b. Copy the custom code into the image by `COPY . /var/www/html`.
    <br>
    c. Install the PHP-MySQL support in the image. `RUN docker-php-ext-install mysqli`. This allows PHP to communicate with MySQL.

3.  To link the PHP application to the MySQL database, which is another container, we need to use docker-compose.
4.  Create a file with name `docker-compose.yaml`. 
5.  Here we specify the parameters of the two containers what we want to start.
6.  
    ```
    version: "3.7"

    services:
      db:
        image: mysql:8.0
        restart: always
        container_name: app_database
        ports:
          - '3307:3306'
        environment: 
          MYSQL_SERVER: db
          MYSQL_DATABASE: app
          MYSQL_ROOT_PASSWORD: 1234
          MYSQL_PASSWORD: 1234
        volumes:
        - app-db:/var/lib/mysql

      php:
        build: .
        ports:
          - '8000:80'
        container_name: app_frontend
        environment: 
        MYSQL_SERVER: db
          MYSQL_DATABASE: app
          MYSQL_USER: root
          MYSQL_ROOT_PASSWORD: 1234
          MYSQL_PASSWORD: 1234
        volumes: 
        - .:/var/www/html 

    volumes: 
      app-db:

    ```
    This will be used create a MySQL container with the name `app_database` and build our PHP image and create a container with the name `app_frontend` and expose it at port 8000. Environment variables are defined so that we can login to MySQL from PHP.

    ### To Create the MySQL Table:
    Now to create the table in the database, exec into the MySQL Container by running `docker exec -it app_database bash`. 

    Inside the container, exec into the MySQL terminal by running `mysql -uroot -p$MYSQL_ROOT_PASSWORD $MYSQL_DATABASE;`. It takes the environment variables from the docker-compose file and lets us login.

    Now to create the `user_info` table, run `CREATE TABLE user_info (first_name varchar(20), last_name varchar(20),phone varchar(10));`.


## Running the Application:

From the directory where `docker-compose.yaml` file is present, run `docker-compose up`. This will bring up both the containers and the app will be accessible from `http://localhost:8000`.
