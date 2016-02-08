docker-symfony
==============

[![Build Status](https://secure.travis-ci.org/eko/docker-symfony.png?branch=master)](http://travis-ci.org/eko/docker-symfony)


Just a litle Docker POC in order to have a complete stack for running Symfony into Docker containers using docker-compose tool.

# Installation

Prerequisites:
* Local PHP (CLI, PHP 5.4+)
* Composer
* Symfony Installer

First, clone this repository:

```bash
$ git clone ...
```

Create Symfony application in `symfony/` using symfony installer:
```bash
$ symfony new symfony/
```

Modify server_name in nginx/symfony.conf to match either the host container or whatever domain will be pointed at the Nginx container. (Default is localhost.)

If desired, change the mapped ports in docker-composer.yml. (Default is 3030 (HTTP) and 3033 (HTTPS).) The default config redirects HTTP requests on the mapped port to 3033. For the redirect to work properly, you also need to change the port in line 6 of nginx/symfony.conf to match your new HTTPS port.

# Setup HTTPS/TLS

To setup HTTPS, you need to create at least three files: nginx/ssl/dhparam.pem, nginx/ssl/nginx.crt, and nginx/ssl/nginx.key.

The dhparam file can be created with this command (WARNING: This takes several minutes!):
```bash
$ openssl dhparam -out nginx/ssl/dhparam.pem 4096
```

To create a private key and self-signed certificate, run:
```bash
$ openssl req -x509 -nodes -days 365 -newkey rsa:4096 -keyout nginx/ssl/nginx.key -out nginx/ssl/nginx.crt
```

OR

You can create a CSR and private key using:
```bash
$ openssl req -new -newkey rsa:4096 -nodes -keyout nginx/ssl/nginx.key -out signing_request.csr
```
The CSR can then be used to obtain a proper certificate and CA bundle file, which need to be renamed nginx.crt and ca_cert_bundle.crt respectively, and moved to nginx/ssl/. Then you need to uncomment the lines directly after the comments starting with CA_CERT_BUNDLE in nginx/Dockerfile and nginx/symfony.conf.

# Docker Compose optional items
The default docker-compose.yml file includes a linked MySQL DB image and ELK (Elasticsearch, Logstash, Kibana) log services. These can be commented out, or replaced, as needed.

# Running
To build the containers, run:
```bash
$ docker-compose build
```

To start the containers, run:

```bash
$ docker-compose up -d
```
(-d detaches from the containers' stdout, leaving you in your shell. Sometimes it's useful to not use -d to see the output for debugging.)

If you kept the default host and ports, you can access your application at `https://localhost:3033/`. The default configuration redirects the non-secured route at `http://localhost:3030/` to the HTTPS page.

To stop all containers, run:

```bash
$ docker-compose stop
```

You are done, you can visit your Symfony application on the following URL: `http://symfony.dev` (and access Kibana on `http://symfony.dev:81`)

_Note :_ you can rebuild all Docker images by running:

```bash
$ docker-compose build
```

# Images

Here are the `docker-compose` built images:

* `application`: This is the Symfony application code container,
* `db`: This is the MySQL database container (can be changed to postgresql or whatever in `docker-compose.yml` file),
* `php`: This is the PHP-FPM container in which the application volume is mounted,
* `nginx`: This is the Nginx webserver container in which application volume is mounted too,
* `elk`: This is a ELK stack container which uses Logstash to collect logs, send them into Elasticsearch and visualize them with Kibana.


# Read logs

You can access Nginx and Symfony application logs in the following directories into your host machine:

* `logs/nginx`
* `logs/symfony`

# Use Kibana!

You can also use Kibana to visualize Nginx & Symfony logs by visiting `http://[localhost-or-container-ip]:81`.
