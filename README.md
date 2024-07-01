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

### Obtain and Store the Access Token in a Bash Variable

1) You can store the token value in a Bash variable named `$TOKEN`. This value can then be embedded in the next query.

```sh
export TOKEN=$(curl -s --location \
--request POST 'http://localhost:8080/realms/master/protocol/openid-connect/token' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--data-urlencode 'client_id=admin-cli' \
--data-urlencode 'username=admin' \
--data-urlencode 'password=admin' \
--data-urlencode 'grant_type=password' | jq -r '.access_token')
```

2) Now you can use the stored $TOKEN value to query information about the realm named `my-realm`.

```sh
curl -s --location --request GET 'http://localhost:8080/realms/my-realm/' \
--header 'Authorization: Bearer $TOKEN' \
--header 'Content-Type: application/json' \
--data-raw '{ "Path": "my-realm" }' | jq
```
![image](https://github.com/al3v/Keycloak/assets/73062283/e84dc3db-2466-4f20-876e-48c0a569515f)

## Roles

After obtaining the `$TOKEN` value, we can view the roles related to the realm named `my-realm`.

```sh
curl -s -X GET http://localhost:8080/admin/realms/my-realm/roles \
-H "Authorization: Bearer ${TOKEN}" | jq .
```
![image](https://github.com/al3v/Keycloak/assets/73062283/09ef8dfd-c8cb-4f6f-bd7d-bca82d02d63c)


## Realms

After obtaining the `$TOKEN` value, you can view all realms.

```sh
curl -s --location --request GET 'http://localhost:8080/admin/realms' \
-H "Authorization: Bearer ${TOKEN}" \
-H "Content-Type: application/json" | jq .
```
![image](https://github.com/al3v/Keycloak/assets/73062283/bbb608fa-cfaa-4ebd-9ccb-fb1aa81b0618)

## Creating a Realm

After obtaining the `$TOKEN` value, a new realm can be created.

```sh
curl --location --request POST 'http://localhost:8080/admin/realms' \
-H "Authorization: Bearer ${TOKEN}" \
-H 'Content-Type: application/json' \
--data-raw '{
    "id": "deneme3",
    "realm": "deneme3",
    "displayName": "deneme3 deneme3",
    "enabled": true,
    "sslRequired": "external",
    "registrationAllowed": false,
    "loginWithEmailAllowed": true,
    "duplicateEmailsAllowed": false,
    "resetPasswordAllowed": false,
    "editUsernameAllowed": false,
    "bruteForceProtected": true
}'
```
![image](https://github.com/al3v/Keycloak/assets/73062283/8ae3d505-5b7c-4dbd-be5d-1be1da9873be)

## Deleting a Realm

After obtaining the `$TOKEN` value, an existing realm can be deleted.

```sh
curl --location --request DELETE 'http://localhost:8080/admin/realms/deneme3' \
-H "Authorization: Bearer ${TOKEN}" \
-H 'Content-Type: application/json' \
--data-raw '{ "realm": "deneme3" }'
```
![image](https://github.com/al3v/Keycloak/assets/73062283/8c538323-de41-4f8c-8f9e-dfff417bbe12)

-------------------------------------------
**PS:**
1) Dont forget to run this to obtain `$TOKEN` value before running every command. If tou are gettinf `"error": "HTTP 401 Unauthorized"
` this is the reason most likely!
```sh
export TOKEN=$(curl -s --location \
--request POST 'http://localhost:8080/realms/master/protocol/openid-connect/token' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--data-urlencode 'client_id=admin-cli' \
--data-urlencode 'username=admin' \
--data-urlencode 'password=admin' \
--data-urlencode 'grant_type=password' | jq -r '.access_token')
```

2) my-realm realm was created after connecting keycloak on local, I just skipped that step
![image](https://github.com/al3v/Keycloak/assets/73062283/7edfb146-9bd8-45e9-84a7-f6593552c6e0)


