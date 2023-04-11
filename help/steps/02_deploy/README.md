#  Deploying Strapi

## Setting up our integration

1. Create a new project

    ```
    platform project:create
    ```

    - Project title: "NodeCongress"
    - Region: Pick somewhere close to you
    - Default branch: main
    - Set as remote: y
    - Continue: y

1. Visit your empty project: https://console.platform.sh/USERNAME_HASH/PLATFORM_SH_PROJECT_ID
1. Generate a new GitHub token (Classic): https://github.com/settings/tokens/new
    - If you're repo is public:
        - `public_repo`
        - `admin:repo_hook`
    - If your repo is private:
        - `repo`
        - `admin:repo_hook`  

1. Integrate the project with the repository:

    ```
    platform integration:add --type github --token GITHUB_ACCESS_TOKEN --repository USERNAME/nodecongress --project PLATFORM_SH_PROJECT_ID
    ```

    Follow the prompts to finish setting up the integration:

    * Build pull requests (--build-pull-requests) **true**
    * Build draft pull requests (--build-draft-pull-requests)? **true**
    * Build pull requests post-merge (--build-pull-requests-post-merge) **false**
    * Clone data for pull requests (--pull-requests-clone-parent-data) **true**
    * Fetch branches (--fetch-branches) **true**
    * Prune branches (--prune-branches) **true**

1. The first activity will fail. Take a look at the resulting error to see what we're missing:

    ```
    Found 2 commits
    
    E: Error parsing configuration files:
        - applications: No `.platform.app.yaml` file found anywhere in the repository.
        - routes: Error loading file: No `.platform/routes.yaml` file found in the repository.
    
    E: Error: Invalid configuration files, aborting build
    ```

## Deployment configuration

1. Create a new branch to work from

    ```
    git checkout -b platformify
    ```

1. Create the missing files:

    ```
    mkdir .platform
    touch .platform/routes.yaml
    touch .platform.app.yaml
    ```

    Make sure the structure is as follows:

    ```
    .
    ├── .git
    ├── .platform
    │   └── routes.yaml
    ├── README.md
    └── api
        ├── ...
        └── .platform.app.yaml
    ```

1. Configure how requests are handled (`.platform/routes.yaml`):

    ```yaml
    "https://www.api.{default}/":
        type: redirect
        to: "https://api.{default}/"
    "https://api.{default}/":
        type: upstream
        upstream: "strapi:http"
        id: "api"   
    ```

1. Configure the application (`api/.platform.app.yaml`):

    ```yaml
    name: strapi
    
    type: 'nodejs:18'
    
    build:
        flavor: none

    dependencies:
        nodejs: 
            yarn: "*"
    variables:
        env:
            NODE_ENV: production
    hooks:
        build: |
            yarn --frozen-lockfile
            yarn build
    
    web:
        commands:
            start: .global/bin/yarn start

    disk: 1024

    mounts:
        'public/uploads':
            source: local
            source_path: uploads
        '/.tmp':
            source: local
            source_path: tmp
    ```

1. Configure the required environment variables in a file called `api/.environment`:

    ```bash
    # Keys
    export APP_KEYS=GzJwXgkYcTpND5tSohWCkA==,q5AiYmxZtYy60wrcjaJulg==,cSL0JHf+o62DqHHsrjEemA==,fYZuJ0TIMTXPWwEu02Vnkg==
    export API_TOKEN_SALT=$PLATFORM_PROJECT_ENTROPY
    export ADMIN_JWT_SECRET=$PLATFORM_PROJECT_ENTROPY
    export JWT_SECRET=$PLATFORM_TREE_ID
    export TRANSFER_TOKEN_SALT=$PLATFORM_PROJECT_ENTROPY:q

    # Database
    export DATABASE_CLIENT=sqlite
    export DATABASE_FILENAME=.tmp/data.db
    ```

1. Commit, push, and open a PR for the changes:

    ```
    git add .
    git commit -m "Platformify"
    git push origin platformify
    ```

1. When the PR check has finished, visit the `pr-1` environment, which has failed with a 502. 
    Since we're building for production, we need to migrate our local data. 

    ```
    platform mount:upload --mount public/uploads --source api/public/uploads
    platform mount:upload --mount .tmp --source api/.tmp
    platform redeploy -e pr-1
    ```
