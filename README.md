# kamal-quake

This repo contains a simple recipe for self-hosting [quakejs](https://github.com/inolen/quakejs) in an https-friendly manner using [kamal 2](https://kamal-deploy.org/). Kamal makes setting up quake with https a breeze - automatically creating / managing a reverse proxy as well as the required self-signed SSL certs using letsencrypt.

A table of contents for recommended setup and deployment using this pattern is below:

- [hosting requirements](#hosting-requirements)
  - [hosting provider](#hosting-provider)
  - [minimum machine specs](#minimum-machine-specs)
  - [docker registry](#docker-registry)
  - [ssh into your machine](#ssh-into-your-machine)
  - [firewall ports](#firewall-ports)
- [kamal deployment](#kamal-deployment)
  - [installing kamal](#installing-kamal)
  - [customizing the config](#customizing-the-config)
  - [deploy](#deploy)
- [multiplayer setup](#multiplayer-setup)
  - [the first screen](#the-first-screen)
  - [keyboard controls](#keyboard-controls)
  - [creating a multiplayer game](#creating-a-multiplayer-game)

## hosting requirements

### hosting providers

Any hosting provider - including a self-hosted machine - is just fine. Kamal makes the setup / deployment process using any available machine - regardless of provider - the same.

### minimum machine specs

A minimum of 1 GB of ram is recommended [by the creators of quakejs](https://github.com/inolen/quakejs).

### docker registry

Kamal 2 currently requires an available registry to push your version of the image to. Any docker registry (e.g., github, dockerhub, etc.,) works fine. All you need is your registry name and login secret.

The Kamal team is working to relax this requirement so that in the future no registry will be required.

### ssh into your machine

After ssh-ing into your machine, create a docker user group

```bash
sudo groupadd docker
```

Next, add your default user to the docker user group

```bash
sudo usermod -aG docker <default user name>
```

### firewall ports

Depending on your choice of usage (http vs https) certain ports must be left open on the security settings / firewall of your hosting instance

- **for http usage:** ports 80 and 27960 must be set open to the web
- **for https usage:** ports 443 and 27960 must be set open to the web

If this is **not** done, you will be stuck in the loading screen of the game.

## kamal deployment

### installing kamal

Once you've setup a hosting machine and docker registry as described above, you are ready to setup / deploy your instance of quakejs (using http or https at your discretion).

First install kamal

```bash
gem install kamal
```

If you need to install ruby first [follow these instructions](https://www.ruby-lang.org/en/documentation/installation/).

### customizing the config

Next pull this repository and adjust its kamal deployment config file, located at

```bash
config/deployyml`
```

This config file is repeated below, with `#TODO` annotations indicating the key-value pairs you must customize. These include the IP address of your machine, your url, docker registry, and docker registry credentials.

```yaml
service: quakejs # <-- (optional) replace with the name of your service
image: <your-username>/<your-image> # TODO
servers:
  web:
    hosts:
      - <IP address of your machine> # TODO
    options:
      publish:
        - "27961:27961"
        - "27960:27960"
      expose:
        - "80"

proxy:
  ssl: true
  host: <your-web-address.xyz> # TODO
  app_port: 80
  healthcheck:
    interval: 3
    path: /
    timeout: 10

env:
  SERVER: <your-web-address.xyz> # TODO
  HTTP_PORT: 80

registry:
  server: <your docker registry> # TODO
  username: <your docker registry username> # TODO
  password:
    - <your docker registry password> #TODO

builder:
  arch: amd64

ssh:
  user: ubuntu
```

### deploy

With the previous steps completed, execute the following at the root of this repository to create your instance of quakejs.

```bash
kamal setup
```

This will pull all requisite images, push your version to your preferred docker registry, setup a reverse proxy for https, and create requisite SSL certs via letsencrypt.
