# Accounts

This project contains components for user (account) management functionality.

## Overview

Back-end services include:

- Keycloak and a REST JSON-API wrapping the Keycloak API and exposing additional functionality
- MySQL database for Keycloak
- Frontend UI in Ember.js

## Building the module locally from scratch

To build from source and run the system locally you need a \*nix host - your own computer, a virtual machine or a server. 

Requirements:
- `docker`
- `docker-compose`
- `make`
- `git`
- Email server, eg. [DINA Mail-docker](https://github.com/DINA-Web/mail-docker)

See bootstrap repository for [details about setting up your host](https://github.com/DINA-Web/bootstrap).

## Step-by-step instructions

A recipe of commands to get the necessary parts in place, running on your host.

First add your user to the Docker group, so you can run Docker commands without sudoing..

		sudo usermod -aG docker ${USER}
		
TODO: Check if this may cause security issues

### **1\)** Get latest version of the module

		git clone $THIS_REPO_SLUG
		cd accounts-docker

To use branch other than master, type

		git fetch
		git checkout $BRANCH_NAME

### **2\)** Create secrets

		make secrets

Making secrets again has no effect, as `make` will not overwrite existing file. But if you remove the file and `make` it again, you have to configure email settings again, and make sure that connection between MySQLAdd note 

		firefox https://beta-accounts.dina-web.net

Log in with the default Accounts API user credentials from the 'envapi.template' file that you have used, usually user: admin@nrm.se and pass: admin#001.

## Upgrading the module locally

To use branch other than master, type

		git fetch
		git checkout $BRANCH_NAME

Pull latest changes, regenerate dotfiles from your local secrets and rebuild & start the services. 

		git pull
		make dotfiles
		make
		
Some things might require manual upgrades (e.g. regenerating secrets file, if new random secrets are needed somewhere) - check these from release notes.

## Building on Mac

When building on MacOS, the "envsubst" command used in `make dotfiles` may not be available by default. It can be installed with:

		brew install gettext
		brew link --force gettext 

## Issues

Currently these are some known issues that stops the system from being fully functional upon start. Please see the Issues list of this repository for details.

This setup requires modifying the hosts file. Another option would be to include name server as a service.

# Technical overview of the build & deployment process

1) Pull latest code from Github
2) Use `make secrets` to create random secrets (passwords) to access different services. Add external service passwords manually. Use `make dotfiles` to save these to dotfiles in the env-directory for later use. ()
3) Build the system using `make`. This will read the `Makefile` in the root directory and for each service (sso, api, db, ui) of the module and
   1) Gets a Docker image from Docker Hub, and runs it as a Docker container (and installs build tools). This is then uses to build/package the code into a working service. [1]
   2) Calls the service's `Makefile` to build the service into a Docker image. Settings for this come from the service's `Dockerfile`. Tags the image with "dina/$SERVICENAME:$VERSIONNAME"
4) Run the module by calling `docker-compose`. This starts all the services as Docker containers from their images (created an tagged on previous step), using settings from `docker-compose.yml`. Docker-compose will also 
   - Create a network (and links) betweeen the services, e.g. a backend application and it's database
   - Set environment variables from dotfiles or from `docker-compose.yml` 
   - Open ports between services and outside world
   - Map volumes for ... **TODO**
(Currently this step is automatically included in the make command.)

[1] Building/packaging means e.g.
- Packaging Java code into JAR-package
- Building Ember project into minified files

# Debugging

To access Keycloak admin ui, add this to docker-compose.yml, under `sso`:

    ports:
      - "8080:8080"

Then do `docker-compose down sso` and `docker-compose up -d sso`. (`docker-compose restart sso` is not enough)

Remove the port binding after you are done debugging.
