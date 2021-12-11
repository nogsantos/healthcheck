# Healthcheck

The [healthcheck](https://docs.docker.com/compose/compose-file/compose-file-v2/#healthcheck) property was originally introduced in the 2.1 Compose file format and is now part of the [Compose Specification](https://github.com/compose-spec/compose-spec/blob/master/spec.md) used by recent versions of Docker Compose.
This allows a check to be configured in order to determine whether or not containers for a service are "healthy."

## How can I wait for container X before starting Y?

This is a common problem and in earlier versions of docker-compose requires the use of additional tools and scripts such as [wait-for-it](https://github.com/vishnubob/wait-for-it) and [dockerize](https://github.com/jwilder/dockerize).
Using the `healthcheck` parameter the use of these additional tools and scripts is often no longer necessary.

## Waiting for PostgreSQL to be "healthy"

A particularly common use case is a service that depends on a database, such as PostgreSQL.
We can configure docker-compose to wait for the PostgreSQL container to startup and be ready to accept requests before continuing.

The following healthcheck has been configured to periodically check if PostgreSQL is ready using the `pg_isready` command. See the documentation for the `pg_isready` command [here](https://www.postgresql.org/docs/9.4/static/app-pg-isready.html).
```yml
healthcheck:
  test: ["CMD-SHELL", "pg_isready"]
  interval: 10s
  timeout: 5s
  retries: 5
```
If the check is successful the container will be marked as `healthy`. Until then it will remain in an `unhealthy` state.
For more details about the healthcheck parameters `interval`, `timeout` and `retries` see the documentation [here](https://docs.docker.com/engine/reference/builder/#healthcheck).

Services that depend on PostgreSQL can then be configured with the `depends_on` parameter as follows:
```yml
depends_on:
  postgres-database:
    condition: service_healthy
```

## License

MIT License

Referenced post https://github.com/peter-evans/docker-compose-healthcheck