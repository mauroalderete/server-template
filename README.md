# starter-server-template <!-- omit in toc -->

***A minimal and flexible template to quickly deploy a simple server using Docker Compose***

<div align="center">

&nbsp;

[![License: MIT](https://img.shields.io/badge/License-Private-yellow.svg)](./LICENSE)
[![Contributor covenant: 2.1](https://img.shields.io/badge/Contributor%20Covenant-2.1-4baaaa.svg)](./CODE_OF_CONDUCT.md)
[![Semantic Versioning: 2.0.0](https://img.shields.io/badge/Semantic--Versioning-2.0.0-a05f79?logo=semantic-release&logoColor=f97ff0)](https://semver.org/)

[![Labeling](https://github.com/mauroalderete/server-template/actions/workflows/labeling.yml/badge.svg)](https://github.com/mauroalderete/server-template/actions/workflows/labeling.yml)
[![Liberation](https://github.com/mauroalderete/server-template/actions/workflows/liberation.yml/badge.svg)](https://github.com/mauroalderete/server-template/actions/workflows/liberation.yml)
[![Project Automations](https://github.com/mauroalderete/server-template/actions/workflows/project-automation.yml/badge.svg)](https://github.com/mauroalderete/server-template/actions/workflows/project-automation.yml)

[Bug Report](./issues/new?assignees=&labels=bug%2Clifecycle%2Fneeds-triage&projects=mauroalderete%2F20&template=1-bug-report.yml&title=...+is+broken)
⭕
[Feature Request](./issues/new?assignees=&labels=enhancement%2Clifecycle%2Fneeds-triage&projects=mauroalderete%2F20&template=2-feature-request.yml&title=As+a+%5Btype+of+user%5D%2C+I+want+%5Ba+goal%5D+so+that+%5Bbenefit%5D)
⭕
[Help Wanted](./issues/new?assignees=&labels=help+wanted%2Clifecycle%2Fneeds-triage&projects=mauroalderete%2F20&template=3-help-wanted.yml&title=I+need+help+with...)

[![Share on Twitter](https://img.shields.io/twitter/url?label=Share%20on%20Twitter&style=social&url=https%3A%2F%2Fgithub.com%2Fatapas%2Fmodel-repo)](https://x.com/intent/post?text=👋%20Check%20this%20amazing%20repo%20https://github.com/mauroalderete/server-template,%20created%20by%20@_mauroalderete%0A%0A%23DEVCommunity%20%23100DaysOfCode%20%23DevOps%20%23Server)

</div>

&nbsp;

## Table of Contents <!-- omit in toc -->

- [:building\_construction: How to Set up the template](#building_construction-how-to-set-up-the-template)
	- [Code of conduct](#code-of-conduct)
	- [License](#license)
	- [Versioning](#versioning)
	- [Gitignore](#gitignore)
	- [Others](#others)


## :wave: Introducing server-template

A minimal and flexible template to quickly deploy a simple server using Docker Compose. This template includes essential components to set up and manage a lightweight server for various machines, such as Raspberry Pi, personal computers, or other servers in your network.

**🎊 Use this template as a starting point for creating personalized server configurations across your network with ease 🎊**

Features:

- `Traefik` for reverse proxy, routing.
- [gethomepage/homepage](https://github.com/gethomepage/homepage) for a customizable landing page.
- Docker discovery service to easily find and manage Docker containers across machines.
- Default routing, middlewares and host rules for a clean working environment.
- Default configurations for popular services like Plex, Ollama, n8n, ComfyUI, and more.

> [!NOTE]
> We appreciate the great effort they put into [gethomepage/homepage](https://github.com/gethomepage/homepage). Don't forget to visit their repository to learn how to configure your homepage and leave your ⭐ in the process.

## :rocket: How to run your server

The `server-template` is prepared to run in a docker environment using a docker compose file. So, you need to have installed `docker` and `docker-compose` in your machine.

For run the server, you need to clone the repository and enter the folder.

```bash
git clone https://github.com/mauroalderete/server-template
cd server-template
```

Create the docker network `myserver`

```bash
docker network create myserver
```

And run the following command to start the server:

```bash
docker compose up -d
```

## :gear: How to configure the server

### Network

For that the discovery service works and it be able to find the services running in your server, you need to deploy all services in the same network.

For default, the network is called `myserver`. You can change this name in the `docker-compose.yaml` file.

```yaml
networks:
  default:
	name: myserver
	external: true
```

If you change the network name, you need to change the network name in the `docker-compose.yaml` file of the services that you want to deploy in the same network.

### mDNS

This template uses a mDNS hostname `myserver.local` to expose the services. Thats means that you can access the services using the hostname `myserver.local` in your local network or open your browser and visit [http://myserver.local](http://myserver.local).

For the mDNS hostname works, you need to have configure your host to resolve the hostname. For Linux, you can edit the file `/etc/hosts` and add the following line:

```bash
127.0.0.1 myserver.local
```

For Windows, you can edit the file `C:\Windows\System32\drivers\etc\hosts` and add the following line:

```bash
127.0.0.1 myserver.local
```

If you want change the hostname `myserver.local` for other like `myawesomeserver.local` you will need replace all reference into `docker-compose.yaml`, `./homepage/config/*`, and `/etc/hosts` files. A simple search and replace in the project folder will be enough.

> [!NOTE]
> mDNS and the hostname `myserver.local` only works in your local network. If you want to expose the services to the internet, you need to use a domain and configure the DNS.

#### Subdomains

This template is prepared to work with subdomains. You can add a new subdomain in the `/etc/hosts` file and in the urls into `./homepage/config/services.yaml` file.

### SSL support

This tamplates is prepared to work with `https` protocol by default. For easy configuration, the template include two self-signed SSL certificates to `sans` the hostname `myserver.local` and `*.myserver.local`.

We recommend to change the SSL certificates for your own certificates. For that, you need add the SSL certificates in the `./traefik/certs/` folder and configure the `./traefik/dynamic/certs-traefik.yml` file with the correct paths.

```yml
tls:
  stores:
    default:
      defaultCertificate:
        certFile: /etc/traefik/certs/my_new_server_certificate.cert
        keyFile: /etc/traefik/certs/my_new_server_certificate.key
```

If you don't have a SSL certificate, you can use the `mkcert` tool to generate a self-signed certificate. You can follow the instructions in the [mkcert repository](https://github.com/FiloSottile/mkcert).

### Environments variables

Gethomepage uses many files to set up the configuration. The template uses some environment variables to configure gethomepage permissions on his configuration volumes. You can use `.env.example` file to create a `.env` file with your own configuration.

```bash
cp .env.example .env
```

Then, you can edit the `.env` file with your own configuration. The template uses the following environment variables:

```bash
PUID=1000
PGID=1000
```

The `PUID` and `PGID` are the user and group id of the user that will have access to the configuration files. You can get the user and group id of your user with the following command:

```bash
id
```

### Route for discovered services

This template is configured to use the route `/lab` to access the discovered services. IS prepared to discover any service deployed in the same network by docker compose.

> [!WARNING]
> Don't support services deployed in other networks or without docker compose.

When a services is discovered, Traefik checks his labels. If any labels are found, Traefik uses default entrypoints, middlewares, and host rules to set up the access to the service.

By default, the route is composed the next way:

```curl
https://myserver.local/lab/<compose_project_name>/<service_name>/
```

- \[`http`|`https`\]: protocol, if the request use `http` traefik redirect to `https`.
- `myserver.local`: mDNS hostname.
- `/lab`: prefix for discovered services, all services are discovered in this route.
- `<compose_project_name>`: the name of the compose project that the service is running.
- `<service_name>`: the name of the service that the service is running.

If you want change the prefix `/lab` for other like `/services` you will need edit the `./traefik/traefik.yml`.

```yaml
providers:
  docker:
    defaultRule: "Host(`myserver.local`) && PathPrefix(`/lab/{{ index .Labels \"com.docker.compose.project\" }}/{{ index .Labels \"com.docker.compose.service\" }}`)"
```

## :hammer: How to customize the server

The template uses gethomapege to create a landing page with the services that you have in your server. You can customize the landing page editing the `./gethomepage/config/settings.yaml` and `./gethomepage/config/services.yaml` mainly. If you want know more about how to customize the landing page, you can visit the [gethomepage repository](https://github.com/gethomepage/homepage).

## :building_construction: How to Set up a fixed service

A fixed service is a service that you want to run in your server and always be available.

First, you need run the service in the same network that the discovery service is running. You can use the `networks` section in the `docker-compose.yaml` file to add the service to the network.

```yaml
networks:
  default:
	name: myserver
	external: true
```

Additionaly, you need add the traefik labels to set up a fixed route to the service. You can use the following example:

```yaml
services:
	plex:
		labels:
			- "traefik.enable=true"
			- "traefik.http.routers.plex.rule=Host(`plex.myserver.local`)"
			- "traefik.http.routers.plex.entrypoints=websecure"
			- "traefik.http.routers.plex.tls=true"
			- "traefik.http.routers.plex-insecure.rule=Host(`plex.myserver.local`)"
			- "traefik.http.routers.gethplex-insecure.entrypoints=web"
			- "traefik.http.services.plex.loadbalancer.server.port=32400"
```

In this example, the service is a Plex server. Use a subdomain `plex.myserver.local` to access the service. The `traefik.http.routers.plex.rule` is the hostname that the traefik will use to route the request to the service. The `traefik.http.routers.plex.entrypoints` is the entrypoint that the traefik will use to route the request. The `traefik.http.routers.plex.tls` is the flag that the traefik will use to enable the SSL. The `traefik.http.services.plex.loadbalancer.server.port` is the port that the traefik will use to route the request to the service.

Once you have it, you need to configure the service into the services list in the `./gethomepage/config/services.yaml` file. You can use the following example:

```yaml
- WORKSTATIONS:
  - PLAY:
    - Plex:
        icon: plex.png
        href: "https://plex.myserver.local"
        description: "Play your movies, music, TV shows and photos."
        server: my-docker
        container: plex
        ping: plex.myserver.local
        widget:
          type: plex
          url: http://plex.myserver.local:32400
          key: <you plex token>
          fields: ["streams", "albums", "movies", "tv"]
```

In this example, the service is a Plex server. Use a subdomain `plex.myserver.local` to access the service and `plex` is the name of the container. The `ping` is the hostname that the discovery service will use to check if the service is running.

Notes, you can add a widget to the service too. The widget is a small box that shows some information about the service. In this example, the widget is a Plex widget that shows the number of streams, albums, movies, and tv shows that are available in the Plex server.

## :building_construction: How to Set up a discovery service

A discovery service is a service that you want to run in your server and be discovered by the discovery service.

First, you need run the service in the same network that the discovery service is running. You can use the `networks` section in the `docker-compose.yaml` file to add the service to the network.

```yaml
networks:
  default:
	name: myserver
	external: true
```

Additionaly, you need add the minimals labels for the discovery service of the gethomepage to be able to find the service. You can use the following labels:

```yaml
services:
	<service_name>:
		labels:
		- homepage.group=LAB
		- homepage.name=Ollama
		- homepage.href=https://myserver.local/lab/<compose_project_name>/<service_name>/
```

In this example, the service is a Ollama server. The `homepage.group` is the group that the service will be listed in the landing page. The `homepage.name` is the name of the service that will be listed in the landing page. The `homepage.href` is the url that the service will be accessed in the landing page.

Notes that the `homepage.href` is defined by default by Traefik. The url is composed by a harcoded prefix `lab`, the compose project name of the service taht you want to be discovered, and his service name.

The group must be defined in the `./gethomepage/config/services.yaml` file previously.

The `lab` prefix can be changed in the `./traefik/traefik.yml` when the defaultHost for Docker provided is defined.

## Code of conduct

`/CODE_OF_CONDUCT.md`

This code is based on the covenant code. He is only required to specify an email address to the community to send his messages. Now, this email is alderete.mauro@gmail.com.

## License

`/LICENSE`

This license is a private personal license redacted by chatGPT. This is only an example. I recommend changing this license to other than to be attached better to your needs.
You can replace it with any Open License offered by GitHub, too.

## Versioning

`/.github/workflows/versioning.yml`

The versioning workflow contains the commands to generate a new release. This release could be attached with a binary file result from them of your project's build.

The file shows you a simple build step and package.

## Gitignore

`/.gitignore`

This file is empty. Replace the content with what you think is more convenient.

## Others

This template uses [nginx](https://nginx.org/en/), [devcontainer](https://containers.dev/), and [gethomepage/homepage](https://github.com/gethomepage/homepage) to easily setup a minimal server... feel free to check out how to configure each component by visiting their documentation sections.
