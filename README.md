# PDPE (PHP Docker Portable Environment)

[![Docker](https://img.shields.io/badge/Docker-ready-blue?logo=docker)](https://www.docker.com/)
[![Docker Image](https://img.shields.io/docker/pulls/jmeiracorbal/pdpe.svg)](https://hub.docker.com/r/jmeiracorbal/pdpe)
[![PHP](https://img.shields.io/badge/PHP-8.3-blueviolet?logo=php)](https://www.php.net/)
[![DevContainer](https://img.shields.io/badge/DevContainer-supported-2ea44f?logo=visualstudiocode)](https://containers.dev/)
![Maintained](https://img.shields.io/badge/maintained-yes-brightgreen.svg)
![Status](https://img.shields.io/badge/status-active-success)
[![Build and Publish](https://github.com/jmeiracorbal/pdpe/actions/workflows/build-and-publish.yml/badge.svg)](https://github.com/jmeiracorbal/pdpe/actions/workflows/build-and-publish.yml)
[![License: MPL-2.0](https://img.shields.io/badge/License-MPL_2.0-brightgreen.svg)](https://opensource.org/licenses/MPL-2.0)

 This project is as a starter kit to work in PHP application environment in Apache, created to allow multiple application to run in a single container.

> The PHP version supported for this project is stablished on php 8.3.

# Getting started

The project has a completely containerized and expandable environment to build, run, and manage PHP applications with the aid of Apache and Docker.

With support for DevContainer, allows to get a clean separation between code and infrastructure. It is designed for the developer who requires a multi-project environment with optional stacks like MySQL, MongoDB, and built-in dashboards to monitor resources.

## Usage considerations

- **Production**: Use this project in the event that your apps are designed to run concurrently or alongside shared infrastructure. There should be a strong justification for each app to be running in the production stack.
- **Development**: You can have multiple unrelated apps hosted in this environment. Mimicking a realistic architecture (monorepo, microservices, etc.) is advised. In the event of multiple instances of this environment being run locally, change the exposed ports in the `.env` file to avoid conflicts.

_Note_: In production, the htdocs folder should be empty because the projects must be defined in your production stack (with Docker). This is only a recommendation. Applications are expected to be exposed through this environment, not hosted inside it.

## Clone and initialize

Clone the repository and build the development environment locally using the compose file:

```bash
git clone https://github.com/jmeiracorbal/pdpe.git
```

```bash
cd pdpe
```

```bash
docker compose -f etc/docker/stacks/development/docker-compose.yml up -d
```

This gives you a feature-rich development environment with Xdebug, MySQL, and phpMyAdmin support. Devcontainers are also supported. Check out the other stacks that are available.

Then open [your appplication](http://localhost:19874) on the first value configured in `APACHE_PORT_RANGE`.

_Note_: When using this project as a starter kit, remove the .git directory after cloning so that it is no longer connected with this repository and copy the .env.example and rename to .env.

## Use the image instead

You may use the Docker image directly constructing your own project with a custom compose file if you prefer not to clone this repository.

If you wish to manage your own codebase and associated configuration independently and use this environment as a service layer, this is the option for you.

Minimal folder structure required:

```
pdpe/
├── .env
├── docker-compose.yml
├── htdocs/
│   └── your-example-app/
│       └── public/
└── apache/
    └── sites-available/
        └── your-example-apache.conf
```

Compose file example:

```yaml
services:
  web:
    image: jmeiracorbal/pdpe:latest
    ports:
      - "${APACHE_PORT_RANGE}:${APACHE_PORT_RANGE}"
    volumes:
      - ./htdocs:/var/www/html
      - ./apache/sites-available:/etc/apache2/sites-available
```

If you want to enable debug environment:

```yaml
    environment:
      - XDEBUG_MODE=develop,debug
      - XDEBUG_CLIENT_HOST=host.docker.internal
```

You can adjust these paths in the volume mappings, but these are the expected defaults in the official image.

**Requirements**:

- In your.env file, define `APACHE_PORT_RANGE` (for instance, 19874-19899).
- Set up your virtual hosts in the apache/sites-available directory.
- Verify that the internal organization within htdocs adheres to the standard, which places each application in a separate folder with a public/entry point.

_Note_: On the development mode, you must be sure that your htdocs contains a valid application structure aligned with host file for apache server conf before building the production image.

# Deployment environment modes

- `stacks/development`: contains ready-to-use override files for development (mysql, mongo, etc.).
- `stacks/production`: intentionally left empty. You can define your own stack here, designed to run prebuilt apps (deployed elsewhere) and mind to use a reverse proxy configuration.

## Development profile

- Deploys bind mounts for htdocs. Your applications live here on development.
- Has debugging capabilities like Xdebug.
- Adds development services using the devContainer or profiles.

You can enable it using compose override files with the optional service stacks.

### Optional service stacks

This project adds extra services via modular override compose files - you can add tools like MySQL and MongoDB or admin UIs with little or no configuration.

Each variant includes the default web service, all profiles are only valid for development profile (`--profile` dev).

Example:

```bash
docker compose -f docker-compose.yml -f docker-compose.override.mysql.yml --profile dev up
```

You can use the defined **examples** to init your development environment:

* [MySQL + PHPMyAdmin Development Environment](./docs/examples/example_mysql_development.md).
* [MongoDB + MongoExpress Development Environment](./docs/examples/example_mongodb_development.md).

Note: if you want to send your environment, check code of conduct to create a new pull-request.

### DevContainer support

This project has optional DevContainer definitions which are included in the folder `.devcontainer` so that you can open the project in VSCode.

- Pre-configured workspace with PHP tools and extensions along with Xdebug.
- Automatically starting composer install.
- PhpStorm Remote Development and VS Code DevContainers.

_Note_: If your IDE requires that there is a `devcontainer.json` file in the root (for example PhpStorm), you can symlink any one of the predefined configs:

```bash
ln -sf .devcontainer/devcontainer.mysql.json .devcontainer/devcontainer.json
```

## Production profile

- Explicit `-profile` flag too, in this case with `prod` as value.
- You shouldn't have any project and volume to `htdocs` because the main idea is to use your own compose files for your applications when you use this project for production environment.

```bash
docker compose --profile prod up
```

# Project structure

This project born with the idea to organize the code and business logic in separated layers:

* System and infrastructure.
* Applications source code.

```text
.
├── etc/                  # System infrastructure files
│   └── docker/
│       ├── apache2/      # Default dockerfile environment configuration using Apache
│       └── stacks/       # Dockerfiles
├── htdocs/               # Location for PHP applications
│   └── myapp/            # Each app has its own subdirectory and public entrypoint
│       └── public/
└── .devcontainer/        # DevContainer definitions
```

_Note_: Generated apps and vhosts are ignored via .gitignore to avoid tracking local-only changes.

**Layers purposes**:

- etc: Contains all environment and system-level configurations like Dockerfiles, Apache vhosts, Compose files…
- htdocs: Application layer where each project should follow a standard layout (public, composer.json, etc.).
- devcontainer: Contains DevContainer definitions for different runtime combinations. 

# About he base Dockerfile: PHP + Apache

This project uses a multi-stage Dockerfile to build reusable PHP environments for both development and production.

_Base image php:8.3-apache_:
- PHP extensions included: `pdo_mysql`, `gd`, `mbstring`, `bcmath`, `pcntl`, `exif`.
- MongoDB support: Installed via pecl install mongodb.
- Composer: Installed from official Composer image.

_Apache configuration_:
- mod_rewrite available.
- Custom virtual hosts from sites-available/*.conf.
- Log forwarding: Apache logs are forwarded to stdout / stderr for docker-logs compatibility.

**Development stage**

- Includes Xdebug with sensible defaults.
- develop,debug modes.
- `client_host=host.docker.internal`
- `start_with_request=yes`

**Production stage**

- Application code is included in the image.
- No volume bindings.
- Same Apache configuration and vhost structure.

## Vhosts and multi-application mapping

This environment is designed to host multiple PHP applications under a single Apache instance.

### Apache legacy directory structure

The layout follows the standard Debian/Ubuntu Apache structure:

- `sites-available`: contains all virtual host definitions
- `sites-enabled`: created automatically with symlinks to enabled hosts

Example:

```text
htdocs/
├── myapp1/
│   └── public/
├── myapi/
│   └── public/

etc/
└── apache2/
│   ├── sites-available/
│   ├── sites-enabled/ (auto-generated inside container on build time)
```

_Note_: sites-enabled only exists inside the container, not on the host.

### Enable a Vhost

To prevent the Dockerfile modifcation every time you add a new site, vhosts are enabled automatically via symlinks on docker image construction. You can see it on this line:

```Dockerfile
RUN for site in /etc/apache2/sites-available/*.conf; do \
      ln -s "$site" "/etc/apache2/sites-enabled/$(basename $site)"; \
    done
```

### Port-based routing

Each application is bound to a dedicated Apache port (internal and external of docker host), to prevent expose unsafe paths like /public.

The project prevents expose paths like:

- http://localhost:19874/myapp1/public

And gets (following the [examples](./examples)):

- http://localhost:19875 (myapp1)
- http://localhost:19876 (myapi)
- http://localhost:19874 (default dashboard)

All vhosts must declare their port explicitly:

```text
Listen 19875
<VirtualHost *:19875>
    DocumentRoot /var/www/html/myapp1/public
    ...
</VirtualHost>
```

### Port configuration

You define the port range in the `.env` file:

`APACHE_PORT_RANGE=19874-19899`

And apply it in the compose file:

```yml
ports:
  - "${APACHE_PORT_RANGE}:${APACHE_PORT_RANGE}"
```

**Why not use ServerName + /etc/hosts?**

The main idea is:
- Use an external DNS and a reverse proxy architecture.
- Prevent modify the host files which not depends of this project.
- Keeps apps truly isolated.
- Enables consistent dev/prod environments.
- Prevents accidental access to vendor, `.env`, or public folder content paths.

# Environment variables

This project uses a `.env` file to configure the exposed ports and service credentials.
These variables are totally unrelated to your PHP applications, and should only be used to configure the platform itself.

```.env
# Apache
APACHE_PORT_RANGE=19874-19899

# MySQL
MYSQL_ROOT_PASSWORD=root
MYSQL_DATABASE=app
MYSQL_USER=user
MYSQL_PASSWORD=pass
MYSQL_PORT=3306

# PhpMyAdmin
PMA_PORT=8080

# MongoDB
MONGO_ROOT_USER=root
MONGO_ROOT_PASSWORD=example
MONGO_PORT=27017

# Mongo Express
MONGO_EXPRESS_USER=admin
MONGO_EXPRESS_PASSWORD=admin123
MONGO_EXPRESS_PORT=8081
```

_Note_: if you don't use mongo or mysql, you can set empty value in the .env file.

### About the .env file location

Compose file automatically finds the file from the directory in which the command is being run.

For DevContainers, PhpStorm, or CI/CD pipelines to work: 
  
- Keep the .env file in the project root (next to your docker-compose.yml) 
- Always RUN docker compose from the root of the project regardless of how many compose files are storing in the repository.

```bash
docker compose -f docker-compose.yml -f etc/docker/stacks/development/docker-compose.override.mysql.yml --profile dev up -d
```

# Use command line for create new applications

If you are using the cloned repository (alternative to building Docker images), there is a helper script that will create new PHP apps with their Apache vhost config, too:

- A new project folder inside htdocs.
- An Apache VirtualHost file under etc/docker/apache2/sites-available, using a template (`templates/apache2/sites-available/vhost.conf.template`).
- An available port automatically with you configured APACHE_PORT_RANGE.

Example of invocation:

```bash
./create-project.sh myapp
```

This will create:

- `htdocs/myapp/public/index.php` file, when folder name `myapp` is declared because is the value indicated to the script. Includes a file with the classic `phpinfo` function.
- `myapp.conf` which points to /var/www/html/myapp/public because you should manage your core outside of the public folder. Keep in mind.
- Automatically assigns a free port and injects it into the vhost.

_Note_: Docker will pick up new vhosts automatically on container restarted; however, you need to build the image to get the symlink workflow logic contained in the Dockerfile. Only exec:

```bash
docker compose down && docker compose up -d --build
```

# Deployment menu

> This feature is under development yet, probably doesn't work as you expected.

This project includes a Makefile with an interactive menu to simplify common deployment workflows without having to memorize commands.

To start:

```bash
make
```

Select the variant that you wish to deploy (i.e., the base environment or make up-mysql, make up-mongo, etc).

- make up / make down ->only the webservice.
- make up-mysql / make down-mysql: web + mysql + phpmyadmin.
- make up-mongo / make down-mongo: web + mongo + mongo-express.
- make clean: down and remove volumes.
- make destroy: This will delete everything you have in docker not in use.

_Note_: this menu is for development only using the dev profile internally. For info on the profiles read Deployment modes.

# Monitoring

To be clear, the main environment port (`19874`) is set to [Glances](https://github.com/nicolargo/glances), a lightweight monitoring dashboard for inspecting system resources in the container.

Glances runs as an internal service and is exposed via Apache, as the default virtual host.

You may replace this service, or you may extend through Glances, if more advanced observability or metrics are required.

# Disclaimers 

Improvements are welcome, but this is a personal project designed to assist my own PHP-based development processes. It's not intended for commercial use, nor is there any business interest associated with it.

Issues might exist and will be handled as time permits, but one should not expect any fixes or changes as certain. 

If you found a bug or have any suggestion, please create an [issue](https://github.com/jmeiracorbal/pdpe/issues) in line with the [code of conduct](./CODE_OF_CONDUCT.md) and [Contributing guide](./CONTRIBUTING.md) of the project.

# License

This project is licensed under the [Mozilla Public License Version 2.0](https://www.mozilla.org/en-US/MPL/2.0/).  
You are free to use, modify, and distribute this stack as long as modifications to this environment remain open under the same license.
