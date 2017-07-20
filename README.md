# Accounts

This project contains components for account management functionality.

## Overview

Back-end services include KeyCloak and a database and a REST API wrapping the KeyCloak API and exposing additional functionality. There is also a front-end component in EmberJS. To run these components as a standalone minimal system, you also need to launch a reverse proxy for web traffic routing and SSL termination.

## Step-by-step instructions

Here is an attempt to provide a short recipe of commands you can use to get the necessary parts in place.

		# backend with REST API
		git clone $THIS_REPO_SLUG
		cd accounts-docker

		# build and run
		make dotfiles
		make

		# access the UI
		firefox https://beta-accounts.dina-web.net

NB: A local build will initially pulls many dependencies (~150+M maven libs for the API, ~1.4G npm packages for the UI) and takes approx 20 minutes depending on Internet connection speed. Re-building is faster, approx a couple of minutes at the most.

### Linux settings

Before building, do the following:

1) Install prerequisites:

- docker-ce
- docker-compose
- maven

2) Add your user to the docker-group:

		sudo usermod -aG docker ${USER}
		su - ${USER}

3) To connect to Internet from within a container in the NRM's network, you might need to do the following:

A) A new daemon file for the docker with the following JSON content in the given path. Then restart Docker service.

		sudo cat /etc/docker/daemon.json
			{
    		"bip": "172.17.0.1/24",
    		"dns": ["172.17.0.1", 172.16.0.9", "8.8.8.8", "172.16.0.7", "8.8.4.4"]
			}

B) Add the line to the following file. Then restart resolvconf service.

		cat /etc/resolvconf/resolv.conf.d/head
		nameserver 172.17.0.1

4) Add the following entries to the `/etc/hosts` file so that the module responds from this address:

		127.0.0.1	beta-accounts.dina-web.net

## Issues

Currently these are some known issues that stops the system from being fully functional upon start.

Please see the Issues list of this repository for details.
