# RateTT

A Java 17 / Spring Boot prototype for running a rating decision through a Camunda Zeebe BPMN/DMN workflow. It integrates REST, PostgreSQL, Kafka and Elasticsearch.

## How the request is processed

1. `POST /rate` accepts `inn` and `capital`.
2. The API stores the request in PostgreSQL.
3. Zeebe executes the configured BPMN process and DMN decision.
4. The result is stored and sent to Kafka for downstream indexing.

Key sources: [REST controller](src/main/java/best/solution/ever/rest/RateRestAPI.java), [application entry point](src/main/java/best/solution/ever/Application.java), and BPMN/DMN definitions in [resources](src/main/resources/).

## Local development

Prerequisites: Java 17, Maven, Docker Compose and a POSIX-compatible shell for these commands.

Copy the safe templates, then edit the local database password. **Do not commit the resulting local files.**

```sh
cp docker-compose/services.local.env.example docker-compose/services.local.env
cp docker-compose/connector-secrets.example.txt docker-compose/connector-secrets.txt
```

Start the development infrastructure:

```sh
cd docker-compose
docker compose -f services.docker-compose.yaml --env-file services.local.env up -d
cd ..
```

Export the same local environment to the Java process before starting the API:

```sh
set -a
. ./docker-compose/services.local.env
set +a
mvn spring-boot:run
```

`application.properties` reads the database password from `POSTGRES_PASSWORD`, rather than storing a password in the repository. The variables in `services.local.env` are for local development only.

The setup has not been exercised end-to-end as part of this documentation update. The provided Docker Compose file is not a production deployment recipe.

## Security

Older commits in this public repository contained local credentials/configuration values. **Deleting files from the current branch does not erase them from Git history.** If any exposed value was ever used for a real service, rotate it and assess whether history cleanup is necessary. Do not commit credentials into `application.properties` or `docker-compose/`.

## Limitations

This is an example integration, not a verified production rating engine. Its output must not be used to make real financial or eligibility decisions without independent validation.
