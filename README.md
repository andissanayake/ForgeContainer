# Docker Compose Setup for Keycloak and PostgreSQL

This Docker Compose file sets up Keycloak and PostgreSQL, providing an authentication system with data storage. It specifies service configurations, including environment variables for access and a custom network for inter-service communication. Ideal for development, this setup lays the groundwork for application authentication management.

## Services

### PostgreSQL Service (`postgres`):
- **Image:** Uses the Docker image `postgres:16.2` to run a PostgreSQL server.
- **Volumes:** Maps a volume named `postgres_data` to `/var/lib/postgresql/data` inside the container for storage of the database data.
- **Environment Variables:** Configures the database with the name (`POSTGRES_DB`), user (`POSTGRES_USER`), and password (`POSTGRES_PASSWORD`) specified by the environment variables.
- **Networks:** Connects to a custom network `keycloak_network` for communication with the Keycloak service.

### Keycloak Service (`keycloak`):
- **Image:** Utilizes `quay.io/keycloak/keycloak:23.0.6` for running a Keycloak server.
- **Command:** Specifies `start` as the command to run Keycloak.
- **Environment Variables:** Sets up various settings including hostname (`KC_HOSTNAME`), port (`KC_HOSTNAME_PORT`), HTTP configuration (`KC_HTTP_ENABLED`, `KC_HOSTNAME_STRICT_HTTPS`), health check (`KC_HEALTH_ENABLED`), admin credentials (`KEYCLOAK_ADMIN`, `KEYCLOAK_ADMIN_PASSWORD`), and database connection details (`KC_DB`, `KC_DB_URL`, `KC_DB_USERNAME`, `KC_DB_PASSWORD`).
- **Ports:** Exposes port `8080` on the host, mapping it to port `8080` on the Keycloak container for web access.
- **Restart Policy:** Configured to always restart unless manually stopped.
- **Dependency:** Declares a dependency on the `postgres` service, ensuring it starts first.
- **Networks:** Also connected to the `keycloak_network`.

## Volumes

- **postgres_data:** A named volume for storing PostgreSQL data across container restarts, using the default local storage driver.

## Networks

- **keycloak_network:** A custom network using the bridge driver, facilitating communication between the Keycloak and PostgreSQL containers.

## Environment Variables

Specifies the values for database configuration (`POSTGRES_DB`, `POSTGRES_USER`, `POSTGRES_PASSWORD`) and Keycloak admin credentials (`KEYCLOAK_ADMIN`, `KEYCLOAK_ADMIN_PASSWORD`), which are essential for the services to communicate and operate securely.
