# Keycloak
Keycloak is an open-source Identity and Access Management solution aimed at modern applications and services.

## Keycloak Setup with Docker
```sh
docker run -p 8080:8080 -e KEYCLOAK_ADMIN=admin -e KEYCLOAK_ADMIN_PASSWORD=admin quay.io/keycloak/keycloak:23.0.6 start-dev
```
- This command runs a Keycloak server in a Docker container, with the admin username and password both set to "admin", and exposes it on port 8080 of your local machine for development purposes.

Go to `http://localhost:8080/` to access Keycloak
