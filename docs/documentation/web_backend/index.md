# Structure and Concepts
This document tries to explain the thoughts behind the code, and the principles
that were used. If you want to start getting to know the codebase, you should 
start here and at least understand the responsibilities of each part,
so you know where to look for, when you need it.

The following chart provides a rough overview of the project structure.

<a href="../../../assets/images/backend_web_component_diagram.png">![backend web components](../../assets/images/backend_web_component_diagram.png)</a>

## Technologies

The main libraries that are used in the backend are [Laravel](https://laravel.com/)
and [Vue](https://vuejs.org/). Laravel is used for all of the data handling and
authentication, while Vue is used for the dashboard.

For the database, the backend uses MongoDB with 
[Laravel MongoDB](https://github.com/jenssegers/Laravel-MongoDB) as an interface 
between the MongoDB and Laravel Eloquent Models.

[Lighthouse](https://lighthouse-php.com/) is used as an GraphQL server. This 
enables us to use the `schema.graphql` file, where all GraphQL related information 
is stored, in both the backend and the app.

## OAuth

OAuth is used to authenticate requests made to the APIs of the backend. With OAuth you
will need to follow a specific available "flow", which is used to obtain an access
token (instead of sending a raw password the whole time), the central thing in OAuth.
Read more about OAuth and possible flows [here](https://oauth.net/2/) or take a look at 
[Larvel Passport](https://laravel.com/docs/6.x/passport), which is the library used to handles all things 
related to OAuth.  

Since the authentication is already implemented, you don't really
have to interact with Passport while maintaining the app. If you choose to add new features
that require access to the API, for example to analyze data outside of php, everything
you need to know is written down in the 
[Laravel Passport Documentation](https://laravel.com/docs/6.x/passport).  

The flow, that is used by the app, to request the access token is called "Authorization Code with 
PKCE Flow". You can find more information about that [here](https://developer.okta.com/blog/2019/08/22/okta-authjs-pkce/).

!!! info "Passport settings"
    Most of the passport related settings, like creating a new access token or a new
    client, can be done via the dashboards "Manage OAuth" tab.

## GraphQL API

The GraphQL API is used for type-safe data distribution, while it is also the endpoint 
to accept surveillance data from the app.

The respective GraphQL endpoint is found at: `SERVER-ADDRESS.TLD/graphql`
and is secured by authentication, thus **not available without an account**.

!!! tip "Debugging and Testing the GraphQL API"
    If you want to test the Endpoint with other clients e.g. for debugging etc, you
    may create an API-Token within the Dashboard, under `OAuth` --> `Personal access tokens`.

    You will need to copy the token and put it in the `Authorization` header like this:  
    `Authorization: Bearer YOURLONGTOKENBELONGSHERE....XYZ`.

### Schema
The structure and all available queries / mutations can be found in the `/graphql` 
folder or can be viewed with a GraphQL client (Note: See above for authentication). 

You can also explore the GraphQL API with [GraphQL Voyager](https://apis.guru/graphql-voyager/).
To use it, you will need to generate the full schema, without extra library agnostic directives and 
copy that into Voyager under "Change Schema"->"SDL". You can do that by running:
```sh
php artisan lighthouse:print-schema
```

Get more information on how to create new mutations for surveillance data [here](MISSING LINK).
If you want to learn more about the GraphQL library and how to use it, take a look at their documentation [here](https://lighthouse-php.com/)

## REST API

Next to the GraphQL API there is also a REST API. The REST API although should only be used
for data transfer between Laravel and the Dashboard and for OAuth related operations. At the
moment the REST API provides routes that are used for retrieving and updating settings from
the dashboard. These routes could also be replaced with GraphQL queries and mutations, but are
not complex enough to justify this.  

To sum up, the REST API is only needed when you want to add
further settings pages that need to modify/retrieve values in/from the API.

### Available routes:

!!! note "Inspect all routes that are provided by the backend"
    ```sh
    php artisan route:list
    ```

### Public Routes (not internally used / only admin accessable)
#### Account deletion
This is used to let users delete their own account at any given time.
They just need to make a call to this endpoint with a valid authorization mechanism and their account will be deleted.

POST: `/auth/delete`

### Vue / Dashboard

The basic structure of the dashboard is defined via Laravel blade files.
These files are located in the `resources/views` folder. Like Vue components
blade files inherit from eachother, more on this can be read in the 
[blade documentation](https://laravel.com/docs/6.x/blade).
Some parts of the dashboard are written using [Vue]()https://vuejs.org/. Vue 
is setup in `resources/js/app.js`. All of the Vue components life in 
`resources/js/components`. If you want to add a component, you first have 
to export it in `index.js` file and then register it as a component in 
the `app.js` file.