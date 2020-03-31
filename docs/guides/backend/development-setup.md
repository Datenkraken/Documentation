# Development Setup

### Manual Setup

You will need the following software packages:

- php >= 7.2 (With the **php-mongodb** extension installed & enabled)
- mongodb
- composer 
- npm

!!! warning "Make sure to start the mongodb instance before running the server:"
    ``` sh
    sudo systemctl start mongodb
    ```

#### Updating / Installing the project

First clone / pull the latest changes of the git repository / source code.

If you did not setup the project before, you will need to run the following commands. 

!!! warning 
    On a fresh installation, you will need to first create a .env file:
    ``` sh
    cp .env.example .env
    ```

    And generate an application key:  
    ``` sh
    php artisan key:generate
    ```
	After that you should adjust the settings to fit your development setup, especially the datbase section.

You should run the following commands to finish up the installation and compile all assets.
These commands should also **always** be run after making major updates to the code:

```sh
composer install
npm install
npm run dev
php artisan migrate
php artisan passport:install
```

Additionally, you may want to reset and re-seed the database with the default user / data:
```sh
php artisan migrate:fresh
php artisan db:seed
```

#### Start the server

You can then start the php server with:
```sh
php artisan serve
```

To serve the site on your local network, use:
``` sh
php artisan serve --host 0.0.0.0
```

### Docker Setup

You will need to install `docker` and `docker-compose` in order to run this project.
Then execute the following statements to start the project containers.

!!! info "You might need to start the docker service before proceeding"
    ``` sh
    sudo systemctl start docker
    ```

#### Start the containers

Start the containers with the following command:
``` sh
sudo docker-compose up -d
```

!!! warning "After the first start, you will need to execute the following commands once"

    Generate application key:
    ``` sh
    sudo docker-compose exec web sudo php artisan key:generate
    ```
    Generate default OAuth Clients / Secrets:
    ``` sh
    sudo docker-compose exec web sudo php artisan passport:install
    ```

#### Updating / Rebuilding the container

If you updated the project you will need to rebuild the container with the following command:

``` sh
sudo docker-compose build
```


#### Stop / Remove containers

To stop the containers, you can use:
``` sh
sudo docker-compose stop
```

To remove all containers, volumes / persistent storages:
``` sh
sudo docker-compose down -v --remove-orphans
```

### Connect / Use the dashboard

The project should now be available at localhost (127.0.0.1) on port 8000 (when using the manual method) or port 5454, when using docker.

The default admin credentials are:
``` yaml
E-Mail:     admin@admin.com
Password:   password
```

!!! warning "These credentials are only available if you seeded the database"
	You can seed the database with the `php artisan db:seed` command.