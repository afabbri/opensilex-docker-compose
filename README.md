

# Minimalist opensilex docker compose environment

Docker compose environnent to deploy opensilex stack based on a previous work environment variables [opensilex-phis-igepp](https://github.com/p2m2/opensilex-phis-igepp)

  - [Installation](#installation)
    - [Customize hostname](#customize-hostname)
    - [create an administrator user](#create-an-administrator-user)
## Installation

- Clone this repository :

```bash
 git clone --branch 1.0.0-rc+3.1 https://github.com/OpenSILEX/opensilex-docker-compose
```
  
You must run docker compose up command :

```bash
docker-compose up
```

PS: If you want to start docker-compose in daemon mode, run the following command : ``docker-compose up -d ``


This will start the docker-compose stack.


- Create an administrator user

Open a new terminal

```bash
docker exec -it opensilex ./bin/opensilex.sh user add --admin --email=admin@opensilex.org --lang=fr --firstName=firstName --lastName=lastName --password=admin
```

### Test application

Wait few seconds and try to load http://localhost:8081 Opensilex application home page

By default, differents availables services can be found at these adresses :

- Web :
  - Opensilex application : http://localhost:8081
  - RDF4J Workbench  :  http://localhost:8080
  - MongoDB port : http://localhost:27017
### Customize docker port 

Configure  ``docker-compose.yml`` file to configure opensilex stack ports
### Customize config file

Configure config/opensilex.yml file to configure opensilex stack

### Customize hostname

Update host parameters if needeed on docker-compose.yml and  opensilex.env files.
*TODO*

## Manage docker

In order to manage your docker stack via an web interface, we suggest you to use [Portainer CE edition](https://docs.portainer.io/start/install?_ga=2.152302650.2059608137.1662022383-1714209046.1662022383)

## Acknowledgments

Olivier Fangi & Co - [P2M2 Team](https://github.com/p2m2)