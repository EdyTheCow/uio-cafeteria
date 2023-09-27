# 📚 About 
Originally assigned as programming task in python, was converted to javascript using VueJs framework. The application is almost exact replica of the original task, but with added web interface instead of terminal interface.

Furthermore, detailed documentation of application's prod and dev enviroment is provided. GitHub actions are triggered on commit, which create and publish docker image on this repository.

Commits also trigger Netlify which pulls the latest code from this repository, builds and runs the application in production accessable to public.

# 🏗️ Deployment
Instructions for deploying the application. Pick one of the three alternatives:

- Building locally
- Deploying using Docker Compose (dev and prod)
- Deploying to Netlify

## Building locally
<b>Clone repository</b><br />
```
git clone https://github.com/EdyTheCow/uio-cafeteria.git
```
<b>Launch dev preview</b><br />
```
npm install -g pnpm && npm run dev
```
<b>Access the application</b><br />
Navigate to `localhost:5173` in your browser to access the preview


## Deploying using Docker Compose
- The prod environment uses Traefik as reverse proxy to provide certificates and Nginx as web server
- The dev environment launches into dev preview for rapid development

<b>Clone repository</b><br />
```
git clone https://github.com/EdyTheCow/uio-cafeteria.git
```

### Dev enviroment
<b>Launch dev preview container</b><br />
Navigate to `dev-env` and run:
```
docker compose up
```
<b>Access the application</b><br />
Navigate to `localhost:3333` in your browser to access the preview

If container is launched on a external server, SSH tunnel can be used to access the dev preview. Easiest way to do this is to set network to host so you're accessing host locally rather than container. That way you don't have to worry about container changing IP.

### Prod environment
Production environment uses docker image built on top of Nginx using Github Actions. Whenever there's a change in the repository, a new updated image is built. Watchtower can be used to automate the fetching of new docker images in prod environment to automatically push updates. The image can be found in the repository at `Dockerfile`. Alternatively the application could be also run from any environment using `ghcr.io/edythecow/uio-cafeteria:master` image, which includes built and ready to deploy app.

Traefik reverse proxy will automatically generate the certificates using Let's Encrypt. Additional certificate resolvers can be added, such as Cloudflare by following Traefik docs: https://doc.traefik.io/traefik/https/acme/#providers

<b>Change enviroment variables</b><br />
Navigate to `prod-env/compose/.env` file and change the `DOMAIN` variable. Available variables:
| Variable | Example | Description |
|-|:-:|-|
| COMPOSE_PROJECT_NAME | prod_uio-cafeteria | Docker compose container prefix |
| DATA_DIR | ../data | Location where data is stored |
| DOMAIN | example.com | Domain to access the application |
| CF_API_EMAIL | - | Cloudflare email (only used if CF is cert resolver) |
| CF_API_KEY | -| Cloudflare global API key (only used if CF is cert resolver) |

<b>Set correct acme.json permissions</b><br />
Navigate to `prod-env/data/traefik` and run:
```
sudo chmod 600 acme.json
```
This file will store the generated certificates.

<b>Launch prod environment</b><br />
Navigate to `prod-env/compose` and run:
```
docker compose up
```

<b>Access the application</b><br />
Navigate to the `DOMAIN` in your browser you have set earlier

## Deploying to Netlify
- Fork the repository
- Navigate to https://www.netlify.com
- Add new site, select the repository via GitHub account
- Deploy the app, `netlify.toml` is included so no further configuration is required
