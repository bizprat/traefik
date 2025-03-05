# Traefik Reverse Proxy Manager

## Description
This project sets up a Traefik reverse proxy to manage routing for various services. It includes configurations for SSL using Let's Encrypt and a dashboard for monitoring.

## Setting Up Traefik
1. Ensure Docker and Docker Compose are installed on your machine.
2. Clone this repository.
3. Create a `.env` file in the root directory with the following variables:
   ```
   SSL_EMAIL="sample@example.com" # Used by Let's Encrypt
   DOMAIN_NAME="traefik.yourdomain.com" # Traefik Dashboard
   ```
4. Run the following command to start the services:
   ```bash
   docker-compose up -d
   ```

## Using Traefik Network in Other Projects
To use the Traefik network in other Docker projects, ensure that the services are connected to the `traefik` network defined in the `docker-compose.yml` file. This allows Traefik to route traffic to your services.

## Example Configuration for other service
```yaml
services:
  sample-site:
    image: nginx:alpine
    container_name: sample-site
    restart: always
    volumes:
      - ./html:/usr/share/nginx/html:ro
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.sample-site.rule=Host(`test.example.com`)"
      - "traefik.http.routers.sample-site.entrypoints=websecure"
      - "traefik.http.routers.sample-site.tls=true"
      - "traefik.http.routers.sample-site.tls.certresolver=myresolver"
    networks:
      - traefik
      - internal_network

  database:
    image: postgres:alpine
    container_name: sample-db
    restart: always
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
      POSTGRES_DB: sampledb
    networks:
      - internal_network

networks:
  traefik:
    external: true  # Use your existing traefik network
  internal_network:
    driver: bridge  # Private network for internal services (not exposed to Traefik)

```
