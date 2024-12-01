# kamal-quake

This repo contains a simple recipe for self-hosting [quakejs](https://github.com/inolen/quakejs) in an https-friendly manner using [kamal 2](https://kamal-deploy.org/). Kamal makes setting up quake with https a breeze - automatically creating / managing a reverse proxy as well as the required self-signed SSL certs using letsencrypt.

A table of contents for recommended setup and deployment using this pattern is below:

- [hosting requirements]()
  - [hosting provider]()
  - [recommended minimum machine specs]()
  - [docker registry]()
  - [firewall ports]()
- [kamal deployment]()
  - [customizing the config]()
  - [setup and deployment]()
- [what to do at first play]()
  - [the quakejs load screen]()
  - [keyboard controls]()
  - [creating a multiplayer game]()

## hosting machine requirements

### hosting providers

Any hosting provider - including a self-hosted machine - is just fine. Kamal makes the setup / deployment process using any available machine - regardless of provider - the same.

### minimum machine specs

A minimum of 1 GB of ram is recommended [by the creators of quakejs](https://github.com/inolen/quakejs).

### docker registry

Kamal 2 currently requires an available registry to push your version of the image to. Any docker registry (e.g., github, dockerhub, etc.,) works fine. All you need is your registry name and login secret.

The Kamal team is working to relax this requirement so that in the future no registry will be required.

### firewall ports

Depending on your choice of usage (http vs https) certain ports must be left open on the security settings / firewall of your hosting instance

- **for http usage:** ports 80 and 27960 must be set open to the web
- **for https usage:** ports 443 and 27960 must be set open to the web

If this is **not** done, you will be stuck in the loading screen of the game.

## kamal setup

Once you've setup a hosting machine and docker registry as described above, you are ready to setup / deploy your instance of quakejs (using http or https at your discretion).

First install kamal

```bash
gem install kamal
```

If you need to install ruby first [follow these instructions](https://www.ruby-lang.org/en/documentation/installation/).

Next pull this repository and adjust its kamal deployment config file, located in
