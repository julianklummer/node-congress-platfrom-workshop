#  The Strapi backend

## Requirements

- Node 18.14+ (`node --version`; [Install](https://nodejs.org/en/download))
- yarn 1.22.19+ (`yarn -v`; [Install](https://yarnpkg.com/getting-started/install))
- Git installed ([Install](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git))
- A GitHub account ([Sign up](https://github.com/signup))
- A Platform.sh account ([Create account](https://auth.api.platform.sh/register))
- The Platform.sh CLI installed ([Install](https://docs.platform.sh/administration/cli.html))


## A tour of Strapi

1. Create a starter Strapi backend

    ```bash
    yarn create strapi-app api --quickstart --no-run
    ```

    > **Note:**
    >
    > On Mac OSX, you may need to first run `export SHARP_IGNORE_GLOBAL_LIBVIPS=true` for this command to work.

1. Verify the project has been generated correctly:

    ```
    cd api
    yarn develop
    ```

1. Visit the local development server at http://localhost:1337
1. Create an admin user
1. Create a Collection type - Display name: **Article**
1. Add two fields for the Collection type:
    - Text: **title**
    - Rich text: **body**
1. Create a piece of content. **SAVE**, but don't Publish yet.
    - Title: "Test article"
    - Body: https://lipsum.com/feed/html
1. Test the api (http://localhost:1337/api/articles):

    ```
    {"data":null,"error":{"status":403,"name":"ForbiddenError","message":"Forbidden","details":{}}}
    ```

1. Update permissions for **Articles**:

    - Go to http://localhost:1337/admin
    - Settings > (User & Permissions Plugin) Roles > Public > Article
    - Check off **find**, then **SAVE** the changes
    - Revisit http://localhost:1337/api/articles

    ```
    {"data":[],"meta":{"pagination":{"page":1,"pageSize":25,"pageCount":0,"total":0}}}
    ```

1. Publish the article

    - Go to http://localhost:1337/admin
    - Content Manager > Article > "Test article"
    - **PUBLISH**
    - Revisit http://localhost:1337/api/articles

        ```
        {"data":[{"id":1,"attributes":{"title":"Test article","body":"Lorem ipsum dolor sit amet, consectetur adipiscing elit. Pellentesque elementum enim sed libero placerat sodales efficitur sit amet elit. Pellentesque posuere iaculis ligula eu auctor. Sed posuere nisl id sem semper tincidunt. Fusce sagittis arcu sollicitudin sem convallis vestibulum vel eget leo. Etiam vel tortor in libero maximus iaculis ac ac justo. Quisque suscipit euismod finibus. Ut non diam venenatis massa lobortis fermentum. In imperdiet augue sed euismod volutpat. Vestibulum porttitor lorem sed rhoncus commodo. Pellentesque tempor arcu eget luctus ornare. Vivamus nec tristique tortor. Duis nec volutpat lorem. Pellentesque luctus diam quis dictum cursus. Pellentesque habitant morbi tristique senectus et netus et malesuada fames ac turpis egestas. Proin vestibulum dapibus sem vitae laoreet. Nullam euismod ornare imperdiet.\n\nCurabitur turpis libero, vestibulum at est ac, malesuada pharetra urna. Donec interdum hendrerit vulputate. Duis nec laoreet nisi, sit amet ultricies urna. Sed aliquam ac leo nec ultricies. Sed mollis mollis sapien, id pulvinar elit. Sed et nisi sit amet urna pretium placerat eu rhoncus metus. Vestibulum ac ligula semper massa dignissim pellentesque quis quis ante. Sed sodales eros id risus pulvinar, nec congue lectus fringilla. Nulla facilisi. Vivamus nisi metus, efficitur sit amet feugiat vitae, varius laoreet mauris. Mauris laoreet tellus libero, vitae luctus libero tristique ac. Vestibulum diam odio, egestas a diam vitae, bibendum vulputate enim. Morbi a tempor urna. Donec eu massa vitae nisi aliquet dapibus ac et nisl. Morbi id convallis erat. Sed quis imperdiet est.\n\nProin condimentum leo ipsum. Nunc sed orci tellus. Aenean commodo lobortis bibendum. Aenean et maximus arcu. Pellentesque iaculis scelerisque turpis sit amet tincidunt. Curabitur sit amet neque a erat dignissim volutpat. Suspendisse potenti. Pellentesque habitant morbi tristique senectus et netus et malesuada fames ac turpis egestas. Sed placerat metus metus, id consequat est molestie a. Phasellus consectetur lorem ac tristique vulputate.\n\nNunc porttitor dui a metus semper hendrerit. Nunc vestibulum porttitor lectus, sed volutpat tortor tincidunt sit amet. Suspendisse malesuada enim dolor, vel luctus risus porta id. Proin nisi metus, scelerisque non enim at, posuere commodo odio. In dui neque, pharetra eget porta nec, suscipit nec ipsum. Integer faucibus erat nec est iaculis fermentum. Morbi ut mattis augue, quis auctor quam. In nec venenatis leo. Maecenas vulputate odio eu sodales commodo.\n\nCras vel neque ut lorem sodales convallis a vel erat. Fusce eros erat, iaculis at hendrerit vitae, euismod at nunc. Proin vestibulum auctor gravida. Suspendisse sed venenatis lectus. Pellentesque gravida maximus neque et posuere. Lorem ipsum dolor sit amet, consectetur adipiscing elit. Mauris non erat ut diam ornare viverra. Aliquam erat volutpat.","createdAt":"2023-04-10T14:21:54.050Z","updatedAt":"2023-04-10T14:39:55.654Z","publishedAt":"2023-04-10T14:39:55.650Z"}}],"meta":{"pagination":{"page":1,"pageSize":25,"pageCount":1,"total":1}}}
        ```

1. Clean up this project

    ```
    cd ..
    rm -rf api
    ```

## Setup our project

1. Create a new repository to work from

    ```
    mkdir nodecongress
    cd nodecongress
    git init
    ```

1. Create a new repository on GitHub (`nodecongress`)

    ```
    git remote add origin git@github.com:USERNAME/nodecongress.git
    git branch -M main
    ```

1. Install our helper files for the workshop to the project

    ```
    git clone git@github.com:platformsh/nodecongress23.git help
    rm -rf help/.git
    ```

1. Push to GitHub

    ```
    git add .
    git commit -m "Init commit."
    git push -u origin main
    ```

## Generating the demo backend

1. Regenerate a base backend repository

    ```
    yarn create strapi-app api --quickstart --no-run
    ```

    > **Note:**
    >
    > On Mac OSX, you may need to first run `export SHARP_IGNORE_GLOBAL_LIBVIPS=true` for this command to work.

1. Setup demo collections and data

    ```
    cd api
    rm -rf src/api && rm -rf src/extensions
    cp -R ../help/steps/01_generate_api/api src/api
    cp -R ../help/steps/01_generate_api/components src/components
    cp -R ../help/steps/01_generate_api/extensions src/extensions
    cp -R ../help/steps/01_generate_api/script/ script
    cp ../help/steps/01_generate_api/data.zip .
    yarn add unzip-stream uuid-v4
    node script/seed.js
    ```

1. Verify the admin user at http://localhost:1337. 

    ```
    yarn develop
    ```
    
    Three sets of credentials have been generated for you

    ```
    - Super Admin:
        - email: admin@strapidemo.com
        - password: welcomeToStrapi123

    - Editor
        - email: editor@strapidemo.com
        - password: welcomeToStrapi123

    - Author
        - email: author@strapidemo.com
        - password: welcomeToStrapi123
    ```

1. Explore the newly created Collection types, and additions to the Media Library.

    - Article: http://localhost:1337/api/articles
    - Category: http://localhost:1337/api/categories
    - Page: http://localhost:1337/api/pages
    - Place: http://localhost:1337/api/places
    - Restaurant: http://localhost:1337/api/restaurants
    - Review: http://localhost:1337/api/reviews
    - User: http://localhost:1337/api/users

1. With the backend now created, commit and push to your repo

    ```
    git add .
    git commit -m "Generated Strapi backend."
    git push origin main
    ```

