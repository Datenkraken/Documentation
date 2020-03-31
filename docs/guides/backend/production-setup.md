## Production Setup

For the production environment, we prepared docker containers for the application, to deploy them without hassle on any platform.

### Requirements:
You will need to install [Docker](https://docs.docker.com/install/) and [docker-compose](https://docs.docker.com/compose/install/) in order to run this project.
Then execute the following statements to start the project containers.

!!! info "You might need to start the docker service before proceeding"
    ``` sh
    sudo systemctl start docker
    ```

### Preparation

You will have to either pull the repository with git/upload the source code and build the container locally or you can pull the container image from a registry, if available.  

Choose a folder for all backend relevant files, e.g. `/opt/datenkraken` and go to that directory.

#### Pull from git / upload source code

This will require you to upload the source code of the backend or download the source code from a hosted git repository using `git clone`.

!!! warning "You will need all files! E.g. make sure that a docker-compose.yml and all other folders/files are present."

As soon as you got all files uploaded/downloaded to your directory, you are ready for the next steps.

#### Image from the registry

First, you will need to copy the `docker-compose.production.yml` file found in the backend source code to your directory on the server and rename it to `docker-compose.yml`.

Next, you will need to change a line in the `docker-compose.yml`.
Find the line, that looks like this: `build: .` and replace it with `image: ` + the image location, this might look like this: 
``` yaml
image: registry.git.rwth-aachen.de/datenkraken/backend/master:e70e1a01074b412037cda722b8d53a27ad3781da
```
(For example: This can be found on Gitlab under "Packages->Container Registry", if enabled)

### Next steps
Copy the `.env.prod.example` file and rename it to `.env`.  
This file will contain sensitive information and configuration for the service, like email account settings etc.

You will now need to adjust the values in this file and configure the setup.
Usually you will only need to change the mail settings.

#### Start the containers

!!! warning "You may need to login to the registry first (if you are using the registry method), using `sudo docker login [REGISTRY URL]`"
    This can also be done with a so called "Deploy Token" found e.g. in Gitlab under "Your Project -> Settings -> Repository -> Deploy Tokens".

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

### Reverse Proxy with Traefik

To ensure HTTP -> HTTPS redirects and an automatically renewed Let's Encrypt certificate, we are going to setup a reverse proxy using [Traefik](https://docs.traefik.io/).  
Which can be replaced, by any other reverse proxy / nginx with a custom certificate if needed.

!!! warning "Make sure to open ports 80 and 443, for HTTP and HTTPS on the machine"

Below you can find a template `docker-compose.yml` for a working Traefik setup, to use with the production compose file of the backend.

First you will need to create a new docker network named `proxy`:
``` sh
sudo docker network create proxy
```

Then copy the code below to a new traefik specific directory e.g. `/opt/traefik` and name the file `docker-compose.yml`. **Please adjust the file to your setup and replace all placeholders.**

!!! warning "If you use another path than `/opt/traefik`, make sure, to adjust the paths in the template."

``` yaml
version: '2'
services:
  dockersocket:
    image: tecnativa/docker-socket-proxy
    mem_limit: 16mb
    memswap_limit: 32mb
    security_opt:
      - "label:disable"
    read_only: true
    tmpfs:
      - /run/:size=32K
    environment:
      - "CONTAINERS=1"
    volumes:
      - "/var/run/docker.sock:/var/run/docker.sock:ro"
    networks:
      socket:
    restart: always

  proxy:
    image: traefik:v2.2
    read_only: true
    depends_on:
      - dockersocket

    ports:
    - "80:80"
    - "443:443"

    volumes:
    - "/opt/traefik/acme:/etc/traefik/acme"
    - "/opt/traefik/dynamic_conf.yml:/etc/traefik/dynamic_conf.yml:ro"

    command:
    - "--providers.file.filename=/etc/traefik/dynamic_conf.yml"
    - "--providers.file.watch=true"
    - "--providers.docker=true"
    - "--providers.docker.exposedByDefault=false"
    - "--providers.docker.endpoint=tcp://dockersocket:2375"
    - "--certificatesResolvers.tlsletsencrypt.acme.email=REPLACE_WITH_YOUR_EMAIL"
    - "--certificatesResolvers.tlsletsencrypt.acme.storage=/etc/traefik/acme/acme.json"
    - "--certificatesResolvers.tlsletsencrypt.acme.httpChallenge.entrypoint=web"
    - "--entryPoints.web.address=:80"
    - "--entryPoints.web-secure.address=:443"
    - "--serversTransport.insecureSkipVerify=true"

    labels:
    - "traefik.enable=true"

    networks:
      proxy:
      socket:
    restart: always

networks:
  proxy:
    external: true
  socket:
    internal: true
```

Now copy the data below into a file in the traefik directory called `dynamic_conf.yml`:
``` yaml
# Dynamic configuration

# Enable gzip compression
http:
  middlewares:
    compress-gzip:
      compress: {}
    httpsredirect:
      redirectScheme:
        scheme: https
    securityheaders:
      headers:
        frameDeny: true
        sslRedirect: true
        browserXssFilter: true
        contentTypeNosniff: true
        stsIncludeSubdomains: true
        stsPreload: true
        stsSeconds: 31536000

```

You can now run the following command inside the traefik directory, to start the proxy:
``` sh
sudo docker-compose up -d
```

The reverse-proxy will now take care of requesting a new certificate for the hostname/domain you entered, which may take a couple minutes.

Thats it, the backend should now be available under your configured domain and should be served via HTTPS.

## Post installation

!!! info "Make sure, that you are currently in the right directory (where your `docker-compose.yml` resides in), to execute the following commands."

### Updating / Rebuilding the container

If you updated the project you will need to rebuild/pull the container with the following command:

If you uploaded the source code:
``` sh
sudo docker-compose build
```

Or if you pulled the image:
``` sh
sudo docker-compose pull
```
This will only actually update, if the specified tag on the image (the long hash behind the image location) is updated, otherwise you will have to change the
`image: YOURCONTAINERIMAGELOCATION` to the newest tag, before pulling.

!!! warning "You may need to login to the registry first, using `sudo docker login [REGISTRY URL]`"
    This can also be done with a so called "Deploy Token" found e.g. in Gitlab under "Your Project -> Settings -> Repository -> Deploy Tokens".

### Stop / Remove containers

To stop the containers, you can use:
``` sh
sudo docker-compose stop
```

To remove all containers, volumes / persistent storages:
``` sh
sudo docker-compose down -v --remove-orphans
```

### Create an admin account

You can create an admin account with the following command:
```
sudo docker-compose exec web sudo php artisan user:add
```