# Keycloak on Wodby

What Wodby sets up for Keycloak on this service. Keycloak runs from the official `quay.io/keycloak/keycloak` image in production mode (`start`). Configuration is passed as `KC_*` environment variables.

## Linked services

| Link | Variables |
| --- | --- |
| PostgreSQL (required) | `KC_DB_URL_HOST`, `KC_DB_URL_PORT`, `KC_DB_URL_DATABASE`, `KC_DB_USERNAME`, `KC_DB_PASSWORD` |

`KC_DB` is set to `postgres`. All Keycloak data is in that database: the service has no volume.

## What the manifest sets

- `KC_HOSTNAME` is the service's URL in the environment. Keycloak uses it for the addresses it issues, such as token issuers and redirects.
- TLS ends at Wodby's router: `KC_HTTP_ENABLED` is `true` and `KC_PROXY_HEADERS` is `xforwarded`.
- `KC_HEALTH_ENABLED` and `KC_METRICS_ENABLED` are `true`. Health and metrics are served on the management port 9000, which is private; the application port is 8080.

## First administrator

`KC_BOOTSTRAP_ADMIN_USERNAME` and `KC_BOOTSTRAP_ADMIN_PASSWORD` come from the service tokens `admin_username` and `admin_password`. Keycloak uses them only when it creates the master realm, on the first start with an empty database, and marks the account as temporary. Changing the tokens later does not change an existing account.

## Changing configuration

Add or change `KC_*` environment variables on the service and deploy it. Environment variables take precedence over `conf/keycloak.conf`, which is not on a volume. Realms, clients and users are managed in the administration console or its API and stored in the database.

## Check the result

From inside the environment, `/health/ready` on port 9000 is the readiness endpoint the service itself is probed with.
