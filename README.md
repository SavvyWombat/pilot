# Pilot

A lightweight wrapper to help run PHP projects in Docker.

Pilot wraps the `docker-compose` command line tool to allow you to use `.env` files and inject environment variables into your `docker-compose.yml` and `Dockerfile`, making it easier to set up and run projects.

Pilot includes `Dockerfiles` to set up and run PHP 8.1 and 8.2, although with minimal extensions. You can use your own `docker-compose.yml` and `Dockerfiles` configuration with Pilot if you need more than this.

Pilot is designed to help work with Docker containers in development environments, and is not recommended for use in production.

## Getting started

### Requirements

You will need to have Docker and docker-compose installed on your machine for Pilot to work.

### Installation

Pilot is available through Composer:

```
composer require savvywombat\pilot --dev
```

To get started, you can copy the `docker-compose.yml` file shipped with Pilot:

```
./vendor/bin/pilot install
```

Or you can use your own `docker-compose.yml` to configure the services you need for your project.

## Environment variables

If you have a `.env` file in the same location as your `docker-compose.yml`, Pilot will import them, allowing you to use them in `docker-compose.yml` and `Dockerfiles`.

Pilot uses the following environment variables with its default configuration:

* WWWGROUP - the ID of the group to use for file permissions (defaults to the current user's group)
* WWWUSER - the ID of the user to use for file permissions (defaults to the current user)
* NODE_VERSION - the version of node installed on the default pilot service (defaults to 18)
* HTTP_PORT - the external port to access the website hosted by the default pilot service (defaults to 80)
* VITE_PORT - the external port to access the vite server hosted by the default pilot service (defaults to 5173)

### Environment variables in docker-compose.yml

You can use environment variables within your `docker-compose.yml`. It is recommended that you also define a default value to fall back on in case the variable is not defined in your `.env`.

For example, in the default Pilot configuration, we define the ports on the default service like this:

```
    ports:
      - '${HTTP_PORT:-80}:80'
      - '${VITE_PORT:-5173}:5173'
```

If no `HTTP_PORT` has been defined in the environment when starting the services with `pilot up`, then the port would default to 80.

However, if you defined `HTTP_PORT=8080` in your `.env`, then 8080 be the port exposed for forwarding.

### Environment variables in Dockerfiles

Similarly, it is possible to use environment variables in your Dockerfiles, so that you can build your services to run with specific configurations.

## Commands

### Installing and building services

Copy the default `docker-compose.yml` from Pilot into your project's root directory.

```
./vendor/bin/pilot install
```

Build the services defined in `docker-compose.yml`. This command is proxy for `docker-compose build` and so will accept the same arguments, such as `--no-cache`.

```
./vendor/bin/pilot build-services
```


### Accessing docker-compose commands

Apart from `build`, all `docker-compose` commands are proxied without modification, and can be used with the same arguments. Using Pilot ensures that environment variables defined in your `.env` are honoured when running these commands.

Run the services, or start them in listening mode:

```
./vendor/bin/pilot up
./vendor/bin/pilot up -d
```

Stop the services.

```
./vendor/bin/pilot down
```

Stop the services and remove any volumes used by them:

```
./vendor/bin/pilot down -v
```

### Building and serving assets and content

To build any assets in your project:

```
./vendor/bin/pilot build
```

To serve content as your develop. 
This command wraps the [PHP built-in webserver] and should not be used as a production webserver.
The command automatically sets the host and port for the webserver, and accepts the root directory and router script arguments:

```
./vendor/bin/pilot serve
./vendor/bin/pilot serve -t public
./vendor/bin/pilot serve -t public public/index.php
```

### Additional commands

Open a terminal session on the main service:

```
./vendor/bin/pilot bash
```

Run a php script on the main service:

```
./vendor/bin/pilot php ...
```

Run a Composer commands on the main service:

```
./vendor/bin/pilot composer ...
```

Run an npm command on the node service (this is by default the main service, but you can define a separate service in your `docker-compose.yml` for Node and set `NODE_SERVICE` to the name of your Node service):

```
./vendor/bin/pilot npm ...
```

## Support

Please report issues using the [GitHub issue tracker](https://github.com/SavvyWombat/pilot/issues). You are also welcome to fork the repository and submit a pull request.

## Licence

This package is licensed under [The MIT License (MIT)](https://github.com/SavvyWombat/pilot/blob/main/LICENSE).

[PHP built-in webserver]: https://www.php.net/manual/en/features.commandline.webserver.php