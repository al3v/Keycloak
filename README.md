# Keycloak
Keycloak is an open-source Identity and Access Management solution aimed at modern applications and services.

## Keycloak Setup with Docker
```sh
docker run -p 8080:8080 -e KEYCLOAK_ADMIN=admin -e KEYCLOAK_ADMIN_PASSWORD=admin quay.io/keycloak/keycloak:23.0.6 start-dev
```
- This command runs a Keycloak server in a Docker container, with the admin username and password both set to "admin", and exposes it on port 8080 of your local machine for development purposes.

Go to `http://localhost:8080/` to access Keycloak

## Obtain Access Token using Keycloak

1) To obtain an access token from Keycloak, use the following `curl` command:

```sh
curl --location --request POST 'http://localhost:8080/realms/master/protocol/openid-connect/token' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--data-urlencode 'client_id=admin-cli' \
--data-urlencode 'username=admin' \
--data-urlencode 'password=admin' \
--data-urlencode 'grant_type=password' | jq
```
`jq` is added at the end to beutify the output format
![image](https://github.com/al3v/Keycloak/assets/73062283/84789030-cabb-48ab-8e98-de1b9bf0a50e)

2) You can filter and extract the access_token value from the JSON response using the following jq command:
```sh
curl --location --request POST 'http://localhost:8080/realms/master/protocol/openid-connect/token' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--data-urlencode 'client_id=admin-cli' \
--data-urlencode 'username=admin' \
--data-urlencode 'password=admin' \
--data-urlencode 'grant_type=password' | jq -r '.access_token'
```
3) You can store the access_token in a Bash script variable as follows:
```sh
ACCESS_TOKEN=$(curl --location --request POST 'http://localhost:8080/realms/master/protocol/openid-connect/token' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--data-urlencode 'client_id=admin-cli' \
--data-urlencode 'username=admin' \
--data-urlencode 'password=admin' \
--data-urlencode 'grant_type=password' | jq -r '.access_token')

echo $ACCESS_TOKEN
```

