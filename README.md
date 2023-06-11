# Traefik setup

This will setup an instance of traefik that I use as reverse-proxy for other docker projects

## Folder structure

```
traefic/
    acme.json
    .env
    docker-compose.yml
```

## Envionment
Note: you don't need to use the environment variables unless you want to enable Traefik's dashboard or use an ad-blocker or Wireguard.

Duplicate the `.env.template` file and rename it to `.env`

```
LETSENCRYPT_EMAIL=
TRAEFIK_HOST=<traefik.domain.com>
TRAEFIK_DASHBOARD_USERNAME=
TRAEFIK_DASHBOARD_PASSWORD=
SERVER_IP=
```

### Environment variables used for Traefik's dashboard
`LETSENCRYPT_EMAIL` is the email used to generate the SSL
`TRAEFIK_HOST` and `TRAEFIK_DASHBOARD_` are used to access Traefik dashboard

To generate the `TRAEFIK_DASHBOARD_PASSWORD`, use following commands

```
sudo apt-get install apache2-utils
htpasswd -nb <username> <secure_password>
```

### Environment variables used to pair Traefik with an Ad-blocker or Wireguard
`SERVER_IP` is used if you use [pi-hole](https://github.com/m1rkwood/pihole-docker) or [adguard](https://github.com/m1rkwood/adguard-docker).
If you're using [Wireguard](https://github.com/m1rkwood/wireguard), use the Wireguard server IP here.

## acme.json

Before running, you need to create a `acme.json` file and change its permissions:

```
touch acme.json
chmod 600 acme.json
```

## Create network

```
docker network create web
```

## Start the container

```
docker-compose build && docker-compose up -d
```

## Enable Traefik's dashboard
To enable Traefik's dashboard, uncomment this line in the `command` section:
```
- "--api.dashboard=true"
```
as well as the entire `labels` section
```
# labels:
    #
    # traefik dashboard
    # disabled by default / uncomment entire labels section + uncomment dashboard related commands above
    # - "traefik.enable=true"
    # - "traefik.http.routers.api.rule=Host(`${TRAEFIK_HOST}`)"
    # - "traefik.http.routers.api.service=api@internal"
    # - "traefik.http.routers.api.middlewares=auth"
    # - "traefik.http.routers.api.entrypoints=websecure"
    # - "traefik.http.routers.api.tls.certresolver=letsencrypt"
    #
    # generate user:password
    # sudo apt-get install apache2-utils
    # htpasswd -nb <username> <secure_password>
    # - "traefik.http.middlewares.auth.basicauth.users=${TRAEFIK_DASHBOARD_USERNAME}:${TRAEFIK_DASHBOARD_PASSWORD}"
```
You'll also need to set the environment variables (`.env` file) as described in a previous section of this documentation.
