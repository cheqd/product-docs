---
description: Developer guide for running the Credential Service
---

# 🔄 Advanced configuration options

## Run as standalone application using Docker Compose

If you want to run the application without any external databases or dependent services, we provide [a Docker Compose file](https://github.com/cheqd/credential-service/blob/main/docker/no-external-db/docker-compose-no-db.yml) to spin up a standalone service.

```
docker compose -f docker/no-external-db/docker-compose-no-db.yml up --detach
```

This standalone service uses an in-memory database with no persistence, and therefore is recommended only if you're managing key/secret storage separately.

The [`no-db.env` file](https://github.com/cheqd/credential-service/blob/main/docker/no-external-db/no-db.env) in the same folder contains all the environment variables necessary to configure the service. (See section _Configuration_ above.)

## Run with external Key Management System (KMS) and/or authentication service using Docker Compose

Construct the postgres URL and configure the env variables mentioned above.

Spinning up a Docker container from the [pre-built credential-service Docker image on Github](https://github.com/cheqd/credential-service/pkgs/container/credential-service) is as simple as the command below:

### **Configure PostgreSQL database**

Configure the environment variables in the [`postgres.env` file](https://github.com/cheqd/credential-service/blob/main/docker/with-external-db/postgres.env):

1. `POSTGRES_USER`: Username for Postgres database
2. `POSTGRES_PASSWORD`: Password for Postgres database
3. `POSTGRES_MULTIPLE_DATABASES`: Database names for multiple databases in the same cluster, e.g.: `"app,logto"`. This sets up multiple databases in the same cluster, which can be used independently for External Veramo KMS or LogTo service.

Then, make the Postgres initialisation scripts executable:

```
chmod +x docker/with-external-db/pg-init-scripts/create-multiple-postgresql-databases.sh
```

### **Start LogTo service**

Configure the environment variables in the [`logto.env` file](https://github.com/cheqd/credential-service/blob/main/docker/with-external-db/logto.env) with the settings described in section above.

Then, run the LogTo service to configure the LogTo application API resources, applications, sign-in experiences, roles etc using Docker Compose:

```
docker compose -f docker/with-external-db/docker-compose-with-db.yml --profile logto up --detach
```

Configuring LogTo is outside the scope of this guide, and we recommend reading [LogTo documentation](https://docs.logto.io/) to familiarise yourself.

### **Start credential-service app**

Configure the environment variables in the [`with-db.env` file](https://github.com/cheqd/credential-service/blob/main/docker/with-external-db/with-db.env) with the settings described in section above. Depending on whether you are using external Veramo KMS only, LogTo only, or both you will need to have previously provisioned these services as there are environment variables in this file that originate from Postgres/LogTo.

Then, start the service using Docker Compose:

```
docker compose -f docker/with-external-db/docker-compose-with-db.yml up --detach
```

### **Running app or LogTo migrations**

When upgrading either the external Veramo KMS or LogTo, you might need to run migrations for the underlying databases.

You can run _just_ the migration scripts using [Docker Compose profiles](https://docs.docker.com/compose/profiles/) defined in the Compose file.

For example, to run Credential Service app migrations on an existing Postgres database (for external Veramo KMS):

```
docker compose -f docker/with-external-db/docker-compose-with-db.yml --profile app-setup up --detach
```

Or to run LogTo migrations on an existing Postgres database:

```
docker compose -f docker/with-external-db/docker-compose-with-db.yml --profile logto-setup up --detach
```

### Build using Docker

To build your own image using Docker, use the [Dockerfile](https://github.com/cheqd/credential-service/blob/main/docker/Dockerfile) provided.

```
docker build --file docker/Dockerfile --target runner . --tag credential-service:local
```
