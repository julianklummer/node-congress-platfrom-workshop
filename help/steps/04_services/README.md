## Switching services

1. `platform environment:branch updates`
1. Verify the new environment in the management console.
1. Update `database.js` (help/steps/04_services/database.js)
1. Update `.platform/services.yaml`

    ```yaml
    # The services of the project.
    #
    # Each service listed will be deployed
    # to power your Platform.sh project.
    dbmysql:
        type: oracle-mysql:8.0
        disk: 512
    ```

1. Managed services overview.
1. Add a relationship to `.platform.app.yaml`

    ```yaml
    relationships:
        database: "dbmysql:mysql"
    ```

1. Update `.environment`

    ```bash
    # Database
    export DATABASE_CLIENT=postgres
    export DATABASE_NAME=$(echo $PLATFORM_RELATIONSHIPS | base64 --decode | jq -r ".database[0].path")
    export DATABASE_HOST=$(echo $PLATFORM_RELATIONSHIPS | base64 --decode | jq -r ".database[0].host")
    export DATABASE_PORT=$(echo $PLATFORM_RELATIONSHIPS | base64 --decode | jq -r ".database[0].port")
    export DATABASE_USERNAME=$(echo $PLATFORM_RELATIONSHIPS | base64 --decode | jq -r ".database[0].username")
    export DATABASE_PASSWORD=$(echo $PLATFORM_RELATIONSHIPS | base64 --decode | jq -r ".database[0].password")

    export DATABASE_POOL_MIN=0
    export DATABASE_POOL_MAX=10
    ```

1. Add `mysql` dependency

    ```
    cd api
    yarn add mysql pg
    ```

1. `git add .`
1. `git commit -m "Add MySQL service."`
1. Verify the (failed deployment)
1. `platform sql -A strapi < help/steps/04_services/foodadvisor.sql`
1. Verify the environment
1. `platform merge updates`
1. `platform sql -e main < foodadvisor.sql`