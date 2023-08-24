# Docker Local Web Server

[![CircleCI](https://circleci.com/gh/adrian-gheorghe/docker-setup.svg?style=svg)](https://circleci.com/gh/adrian-gheorghe/docker-setup)

## Install Docker Engine

**Update the apt package index and install packages to allow apt to use a repository over HTTPS:**

```bash
sudo apt-get update
```

```bash
sudo apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
```

**Add Docker’s official GPG key:**

```bash
sudo mkdir -p /etc/apt/keyrings
```

```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

**Use the following command to set up the repository:**

```bash
 echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

**To install the latest version, run:**

```bash
 sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

V**erify that the Docker Engine installation is successful by running the hello-world image:**

```bash
 sudo docker run hello-world
```

## Manage Docker as a non-root user

**Create the docker group:**

```bash
 sudo groupadd docker
 ```

**Add your user to the docker group:**

```bash
sudo usermod -aG docker $USER
```

You need to log out and log back in so that your group membership is re-evaluated. If you’re running Linux in a virtual machine, it may be necessary to restart the virtual machine for changes to take effect. 

**You can also run the following command to activate the changes to groups:**

```bash
newgrp docker
```

**Verify that you can run docker commands without sudo:**

```bash
docker run hello-world
```

This command downloads a test image and runs it in a container. When the container runs, it prints a message and exits.

If you initially ran Docker CLI commands using sudo before adding your user to the docker group, you may see the following error:

**WARNING:** Error loading config file: `/home/user/.docker/config.json` - `stat /home/user/.docker/config.json: permission denied`

This error indicates that the permission settings for the `~/.docker/` directory are incorrect, due to having used the sudo command earlier.

To fix this problem, either remove the `~/.docker/` directory (it’s recreated automatically, but any custom settings are lost), or change its ownership and permissions.

**Change ownership and permissions:**

```bash
sudo chown "$USER":"$USER" /home/"$USER"/.docker -R
sudo chmod g+rwx "$HOME/.docker" -R
```

## Install Mkcert

Before installing the Mkcert utility, you will need to install the required packages to your server. 

**You can install it with the following command:**

```bash
apt-get install wget libnss3-tools
```

**Once all the packages are installed, download the latest version of Mkcert from Github:**

```bash
wget https://github.com/FiloSottile/mkcert/releases/download/v1.4.3/mkcert-v1.4.3-linux-amd64
```

**After downloading Mkcert, move the downloaded binary to the system path:**

```bash
mv mkcert-v1.4.3-linux-amd64 /usr/bin/mkcert
```

**Set the execution permission to the mkcert:**

```bash
chmod +x /usr/bin/mkcert
```

**Verify the Mkcert version with the following command:**

```bash
mkcert --version
```

## Info

This setup is the base for all other docker based images and setups created by me and should make development using docker containers a lot easier.

Using this base setup you will not have to keep track of ports or hostnames when setting up a new project. 

Using [Traefik](https://traefik.io/ "Traefik") as a
load balancer you'll be able to use port 80 to access all your web containers. 

Also you'll be able to use subdomains of the following form: http://container.localhost.

[Portainer](https://www.portainer.io/) is set up to make container management easier.

**This base setup contains a stack of containers with:**

- Traefik - reverse proxy container that routes all requests to their specific hosts without the need of using other ports than 80 and 443
- mailhog - container
- Portainer - docker container management ui. Interface that allows you to manage local containers, networks and so on. makes it a lot easier
- elasticsearch - docker container
- mariadb - docker container
- phpmyadmin - docker container

### Portainer Login Credentials

- **user:** `admin`
- **password:** `coR34gt{0w@FmG56>Wh^9]lv2_z1C<A:`

## Create Network

**You need to create the traefik-local network:**

```bash
docker network create traefik-local
```

## Build Containers

```bash
docker-compose build
```

### Raise Containers

```bash
docker-compose up -d
```

## Access

After creating the network and running `docker-compose up` you will be able to access the containers using the following addresses:

- https://portainer.localhost
- https://traefik.localhost
- https://mailhog.localhost
- https://elasticsearch.localhost
- https://mariadb.localhost
- https://phpmyadmin.localhost

Install portainer by setting a user/pass and set it to use your local stack and you are good to go!

## Mkcert Cert

**Generate ssl certificate for local development, because this is required for default containers:**

```bash 
mkcert -cert-file ./docker_files/cert -key-file ./docker_files/cert-key portainer.localhost traefik.localhost mailhog.localhost elasticsearch.localhost mariadb.localhost phpmyadmin.localhost kibana.localhost
```

## Export Database

**Export database from mysql container:**

```bash
sudo /usr/bin/mysqldump --complete-insert --all-databases --result-file=/home/sstoyanov/Database/wowtea_local_10_10.sql --skip-add-drop-table --skip-lock-tables --skip-add-locks --user=root --password=root --host=172.20.0.6 --port=3306
```

## Build and Run Local Web Server 

**Build container commands:**

```bash
bin/build
```

**Start all container commands:**

```bash
bin/start
```

**Stop all container commands:**

```bash
bin/stop
```

## Run command to make new ssl for new container
**To add a certificate, run the following command and provide the domain name when prompted:
bin/add_cert_to_traefik**

```bash
bin/add_cert_to_traefik
```