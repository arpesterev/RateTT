# RateTT

A Java 17 / Spring Boot prototype for evaluating a rating decision through a Camunda Zeebe workflow. It combines REST, BPMN/DMN definitions, PostgreSQL persistence, Kafka and Elasticsearch.

## Request flow

1. `POST /rate` accepts a request containing `inn` and `capital`.
2. The API stores the request in PostgreSQL.
3. A Zeebe process runs the configured BPMN/DMN decision logic.
4. The result is saved and sent through Kafka for downstream indexing.

The entry points are [`RateRestAPI.java`](src/main/java/best/solution/ever/rest/RateRestAPI.java) and [`Application.java`](src/main/java/best/solution/ever/Application.java). The checked-in process definitions are in `src/main/resources/`.

## Local infrastructure

The repository includes a multi-service Docker Compose setup under `docker-compose/` for development. From that directory, the correct Compose argument order is:

```sh
docker compose -f services.docker-compose.yaml --env-file services.local.env up -d
```

Review and configure environment variables and dependent services before starting the stack. The compose file is a development setup, not a production deployment recipe.

From the repository root, with a Java 17 JDK and Maven installed:

```sh
mvn package
mvn spring-boot:run
```

These commands are taken from the checked-in configuration. The complete distributed stack has not been started or validated as part of this documentation update.

## Security note

The repository currently tracks local configuration files. **Do not use real credentials or production secrets in tracked files.** Use local untracked configuration and rotate any credentials that have previously been committed. Removing secrets from the latest revision does not remove them from Git history.

## Scope

This is an example integration, not a validated decision engine. The output should not be treated as a production credit or eligibility assessment.
