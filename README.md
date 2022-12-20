# Mern Docker prod

This is dockerized Mern repo (Create React App, Express, MongoDB, Nginx, pm2, Traefik) meant for production environments and client side rendering where static assets are served by Nginx. It uses strategy with two separate images for client and server. Both client and server containers are then routed to subdomain and exposed through Traefik reverse proxy.

## How to use this

- copy files from the root folder to yours project root folder
- copy `docker` folders inside yours `client` and `server` folders
- forward your specific environment variables in `docker-compose.yml`; Note: use either `environment:` key with `.env` file from the root folder or `env_file:` key with separate `.env` files from `client` and `server` folders
- client container handles all routes except server routes `/api, /auth, /public/images` which are handled by server container. This is needed i.e. for Facebook and Google OAuth that need to access server routes directly. Same `/api, /auth, /public/images` routes are ignored by Nginx in `client/docker/default.conf`. You can customize this to your own needs.
- **Important:** you want to build images locally and push them to Docker Hub and pull images on server in `docker-compose.yml` instead of building images directly on server. This is needed because to build images you need 4GB+ RAM while 0.5GB is usually enough to run your app.
- run with `docker-compose up -d`

## Related projects and example deployment

- [https://github.com/nemanjam/traefik-proxy](https://github.com/nemanjam/traefik-proxy) - here you can find complete Traefik deployment with Mern example on this path [traefik-proxy/apps/mern-boilerplate](https://github.com/nemanjam/traefik-proxy/tree/main/apps/mern-boilerplate)

## Deployments tips

#### Copy local `.env` file to server

```bash
# run from traefik-proxy folder
scp ./apps/mern-boilerplate/.env ubuntu@amd1:~/traefik-proxy/apps/mern-boilerplate
```

#### Troubleshooting MongoDB connection running in a container

#### Error 1: `MongoError: Authentication failed`

- Solution: append `?authSource=admin` to connection string
- **Important:** must delete files in `server/docker/mongo-data` volume

```bash
sudo rm -rf ./server/docker/mongo-data/* # doesn't delete files with .
sudo rm -rf ./server/docker/mongo-data/.mongodb
```

- example of a working MongoDB url:

```bash
MONGO_URI_PROD=mongodb://username:$password@mongo-service:27017/db-name?authSource=admin
```

#### Error 2: `MongooseServerSelectionError: getaddrinfo EAI_AGAIN mdp-mongo`

- Solution 1: add default network in `docker-compose.yml` mongo and server services

```yml
networks:
  - default
```

- Solution 2: append `&directConnection=true` to connection string

## References

- [tutorial](https://systemweakness.com/dockerize-a-mern-stack-app-for-production-with-security-in-mind-part-ii-19330e719795) and repository [KaranJagtiani/MERN-Docker-Production-Boilerplate ](https://github.com/KaranJagtiani/MERN-Docker-Production-Boilerplate) by Karan Jagtiani
- tutorial [Dockerizing a React App](https://mherman.org/blog/dockerizing-a-react-app/) by Michael Herman
