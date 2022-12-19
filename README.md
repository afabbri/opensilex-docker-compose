# Minimalist opensilex docker compose environment

<!-- add _target to all external link -->
<head>
  <base target="_blank">
</head>

Docker compose environnent to deploy opensilex stack based on a previous work <a href="https://github.com/p2m2/opensilex-phis-igepp" target="_blank">opensilex-phis-igepp</a>.

- [Minimalist opensilex docker compose environment](#minimalist-opensilex-docker-compose-environment)
  - [Pre-requesite softwares](#pre-requesite-softwares)
  - [Check your installed softwares](#check-your-installed-softwares)
  - [Stack software name with associated versions](#stack-software-name-with-associated-versions)
  - [Installation steps](#installation-steps)
    - [Fresh new install (compose v2)](#fresh-new-install-compose-v2)
  - [Run minimal opensilex docker stack compose](#run-minimal-opensilex-docker-stack-compose)
    - [(First install only) Create an administrator user](#first-install-only-create-an-administrator-user)
  - [Stop docker stack](#stop-docker-stack)
  - [Other tools or customizations](#other-tools-or-customizations)
    - [(Optional) Add a gui for opensilex-docker-mongodb](#optional-add-a-gui-for-opensilex-docker-mongodb)
    - [(Optional) Add a reverse proxy](#optional-add-a-reverse-proxy)
    - [Migration steps from previous versions](#migration-steps-from-previous-versions)
      - [From previous version 1.0.0-rc+5.2 (compose v2)](#from-previous-version-100-rc52-compose-v2)
      - [From previous version 1.0.0-rc+5.1 (compose v1)](#from-previous-version-100-rc51-compose-v1)
      - [From previous version before 1.0.0-rc+5.1 (compose v1)](#from-previous-version-before-100-rc51-compose-v1)
  - [Customize docker configuration](#customize-docker-configuration)
  - [Manage data](#manage-data)
    - [Export (Experimental)](#export-experimental)
    - [Import (Experimental)](#import-experimental)
  - [Manage docker](#manage-docker)
  - [Debug installation](#debug-installation)
  - [Danger Zone](#danger-zone)
    - [Stop docker stack and erase all data (Be sure to delete all data)](#stop-docker-stack-and-erase-all-data-be-sure-to-delete-all-data)
  - [Acknowledgments](#acknowledgments)

## Pre-requesite softwares

Tested Operating system :

[![Ubuntu22.04](https://img.shields.io/badge/Ubuntu-22.04-E95420?style=for-the-badge&logo=ubuntu&logoColor=white)](https://releases.ubuntu.com/22.04/)

[![Debian11](https://img.shields.io/badge/Debian-11-E95420?style=for-the-badge&logo=debian&logoColor=white)](https://www.debian.org/releases/bullseye/releasenotes)

First you need to have these software installed, you can check if they are [installed](#check-your-installed-softwares) :

- [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
- [docker](https://docs.docker.com/install/)
- [Docker Compose](https://docs.docker.com/compose/install/)

## Check your installed softwares

Following commands should work from everywhere in your system without errors:

`git --version`

`docker --version`

`docker compose version`

## Stack software name with associated versions

- Mandatory softwares :

  - RDF4J - 3.7.7
  - MongoDB - 5.0.14
  - OpenSILEX - 1.0.0-rc+6

- Other managements softwares :
  - mongo-express (A web based gui for mongo) - 1.0.0-alpha.4
  - haproxy (web server used as reverse proxy) - 2.6.6

## Installation steps

This docker version is related to <a href="https://github.com/OpenSILEX/opensilex/releases/tag/1.0.0-rc%2B5.2" target="_blank">1.0.0-rc+6 OpenSILEX version</a>

### Fresh new install (compose v2)

Clone the repository to in order to get the project.

```bash
git clone --branch 1.0.0-rc+6 https://github.com/OpenSILEX/opensilex-docker-compose
cd opensilex-docker-compose
```

For migration steps from previous versions, take a look to the [Migration steps from previous version](#migration-steps-from-previous-versions) section

## Run minimal opensilex docker stack compose

- With a terminal go to the project directory (where this readme is located).
- You must run docker compose up command to start your installation:

```bash
docker compose --env-file opensilex.env build --build-arg UID=$(id -u)  --build-arg GID=$(id -g)
docker compose --env-file opensilex.env run --rm start_opensilex_stack
```

- Expected Output:

```bash
[+] Running 3/0
 ⠿ Container opensilex-docker-rdf4j       Running                                                                                                                                              0.0s
 ⠿ Container opensilex-docker-mongodb       Running                                                                                                                                              0.0s
 ⠿ Container opensilex-docker-opensilexapp  Recreated                                                                                                                                            0.0s
[+] Running 1/1
 ⠿ Container opensilex-docker-opensilexapp  Started                                                                                                                                              0.4s
Waiting for mongo to listen on 27017...
Waiting for rdf4j to listen on 8080...
Waiting for opensilex to listen on 8081..
sleeping
sleeping
sleeping
[wait until it starts]
```

This previous action will block your terminal. When the terminal will be accessible again the opensilex app process will be started and ready.

### (First install only) Create an administrator user

Docker volumes are persistent until you remove them. You only need to create once an user.

```bash
docker exec -it opensilex-docker-opensilexapp ./bin/opensilex.sh user add --admin --email=admin@opensilex.org --lang=fr --firstName=firstName --lastName=lastName --password=admin
```

After opensilex start you will be able to access to the application on port <a href="http://localhost:8081/opensilex/app" target="_blank">localhost:8081/opensilex/app</a>.

By default, different available services can be found at these adresses :

- OpenSILEX web application : <a href="http://localhost:8081/opensilex/app" target="_blank">http://localhost:8081/opensilex/app</a>
- OpenSILEX API : <a href="http://localhost:8081/opensilex/api-docs" target="_blank">http://localhost:8081/opensilex/api-docs</a>
- MongoDB port : <a href="http://localhost:28888" target="_blank">http://localhost:28888</a>
- RDF4J Workbench : <a href="http://localhost:28887/rdf4j-workbench" target="_blank">http://localhost:28887/rdf4j-workbench</a>

_PS: At the first connection, you will need to change rdf4j server port to 8080 (internal docker port) in order to access to rdf4j repositories._

Expected configuration :

![rdf4j_first_connection](./images/rdf4j_first_connection.png)

## Stop docker stack

This command will stop the stack.

```bash
docker compose --env-file opensilex.env stop
```

## Other tools or customizations

### (Optional) Add a gui for opensilex-docker-mongodb

This will start the mongo express server that helps you do explore your mongo data on port [localhost:28889/mongoexpress](http://localhost:28889/mongoexpress). You can also use your own robo3t or Mongo Compass App.

```bash
docker compose --env-file opensilex.env run --rm start_opensilex_stack_mongogui
```

![mongo-express-image](./images/mongo-express.png)

### (Optional) Add a reverse proxy

This will start the haproxy as reverse proxy for opensilex instance on port that you want to expose.

It can be configure in the `./opensilex.env` with the variable `HAPROXY_EXPOSED_PORT` (Default to port 80 equivalent to <a href="http://localhost" target="_blank">http://localhost</a>.

```bash
docker compose --env-file opensilex.env run --rm start_opensilex_stack_proxy
```

If you have installed [optional reverse proxy](#optional-add-a-reverse-proxy)

By default, different available services can be found at these adresses. The port might be change depending on your `./opensilex.env` configuration file.

- Web :
  - OpenSILEX web application : <a href="http://localhost/opensilex/app" target="_blank">http://localhost/opensilex/app</a>
  - OpenSILEX API : <a href="http://localhost/opensilex/api-docs" target="_blank">http://localhost/opensilex/api-docs</a>
  - RDF4J Workbench : <a href="http://localhost/rdf4j-workbench" target="_blank">http://localhost/rdf4j-workbench</a>

if you add installed [(Optional) Add a gui for opensilex-docker-mongodb](#optional-add-a-gui-for-opensilex-docker-mongodb)

- MongoDB express : <a href="http://localhost/mongoexpress" target="_blank">http://localhost/mongoadmin</a>

### Migration steps from previous versions

First, go to the previous directory and get the actual version of the repository.

```bash
# Go inside opensilex-docker-compose directory
git checkout 1.0.0-rc+6
```

#### From previous version 1.0.0-rc+5.2 (compose v2)

If you had a previous installation go to the directory where the project have been clone.
And execute the following command to remove previous docker stack :

```bash
# Remove old containers
docker stop mongodb && docker rm mongodb
docker stop opensilexapp && docker rm opensilexapp
docker stop rdf4jdb && docker rm rdf4jdb
docker stop haproxy && docker rm haproxy
docker stop mongoexpressgui && docker rm mongoexpressgui
```

#### From previous version 1.0.0-rc+5.1 (compose v1)

If you had a previous installation go to the directory where the project have been clone.
And execute the following command to remove previous docker stack :

```bash
# Remove old containers
docker stop mongodb && docker rm mongodb
docker stop opensilexapp && docker rm opensilexapp
docker stop rdf4jdb && docker rm rdf4jdb
docker stop haproxy && docker rm haproxy
docker stop mongoexpressgui && docker rm mongoexpressgui
```

#### From previous version before 1.0.0-rc+5.1 (compose v1)

If you had a previous installation go to the directory where the project have been clone.
And execute the following command to remove previous docker stack :

```bash
# Remove old containers
docker stop mongodb && docker rm mongodb
docker stop opensilex && docker rm opensilex
docker stop rdf4j && docker rm rdf4j
```

## Customize docker configuration

Configure `opensilex.env` file to configure opensilex sparql config, applications ports, applications volumes

```bash
# CAN BE MODIFIED BY USER

## START COMMAND can had debug option by uncommenting the following statment
#OPENSILEX_START_CMD_DEBUG=--DEBUG

# SPARQL
BASEURI=http://opensilex.test/
BASEURIALIAS=opensilex-test
# customize path prefix Ex : localhost:8081/opensilex or localhost:8081/phenotyping_si
OPENSILEX_PATH_PREFIX=opensilex

# FILE SYSTEM
# Default value is "gridfs" - Only gridfs or local are supported
OPENSILEX_FILESYSTEM=gridfs
DATAFILE_OPENSILEX_FILESYSTEM=gridfs
DOCUMENTS_OPENSILEX_FILESYSTEM=gridfs   
# If "local" file system is choosed OPENSILEX_LOCAL_FILE_SYSTEM_DIRECTORY is mandatory if you choose gridfs local will be not used
# File system configuration can be customized to opensilex-template.yml
OPENSILEX_LOCAL_FILE_SYSTEM_DIRECTORY=./opensilex_data
#Ex :
#OPENSILEX_LOCAL_FILE_SYSTEM_DIRECTORY=/home/charlero/GIT/GITLAB/opensilex-docker-compose/dump_scripts/demo_dump/publictest

# PORTS
HAPROXY_EXPOSED_PORT=80
OPENSILEX_EXPOSED_PORT=8081
RDF4J_EXPOSED_PORT=28887
MONGO_EXPOSED_PORT=28888
MONGO_EXPRESS_EXPOSED_PORT=28889

# VERSIONS
HAPROXY_IMAGE_VERSION=2.6.6
OPENSILEX_RELEASE_TAG=1.0.0-rc+6
RDF4J_IMAGE_VERSION=3.7.7
MONGO_IMAGE_VERSION=5.0.14
MONGO_EXPRESS_IMAGE_VERSION=1.0.0-alpha.4
```

## Manage data

File system management are not shown in the the following steps because local files are manage with bind volumes  or with gridfs. Other file systems are not managed in this version of opensilex docker.

### Export (Experimental)

This script will dumps mongodb and rdf4j data in a directory with this structure.

```bash

# Step 1
cd <opensilex-docker-compose-dir>/dump_scripts
# Example directory structure <opensilex-docker-compose-dir>/dump_scripts/dump_example_structure
# ├── mongodb
# │   └── opensilex-docker-db-2022-11-21
# └── rdf4j
#     └── opensilex-docker-db-2022-11-21
sh export_data.sh <path_to_data>  
```

### Import (Experimental)

This script will restore mongodb and rdf4j data in a directory with this structure.

```bash
# Step 1
cd <opensilex-docker-compose-dir>/dump_scripts
# Example directory structure <opensilex-docker-compose-dir>/dump_scripts/dump_example_structure
# ├── mongodb
# │   └── opensilex-dump-db-2022-11-21
# └── rdf4j
#     └── opensilex-dump-db-2022-11-21
sh import_data.sh <path_to_data> 
```

## Manage docker

In order to manage your docker stack via an web interface, we suggest you to use <a href="https://docs.portainer.io/start/install/server/docker/linux#deployment" target="_blank">Portainer Community edition edition</a>

![portainer-image](./images/portainer.png)

## Debug installation

This command will give you stack trace of the docker build.

```bash
docker compose --env-file opensilex.env build > docker_logs/debug.log
```

## Danger Zone

$\color{red}{The\ following\ commands\ may\ produce\ a\ loss\ of\ data}$

### Stop docker stack and erase all data (Be sure to delete all data)

This command will give you stack trace of the docker build.

```bash
docker compose --env-file opensilex.env down --volumes 
# this command will erase all the data
```

## Acknowledgments

Olivier Fangi & Co - <a href="https://github.com/p2m2" target="_blank">P2M2 Team</a>
