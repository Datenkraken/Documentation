# Database

[MongoDB](https://www.mongodb.com/) is the database used by the [web backend](./web_backend/) to store the user data.
It consists out of 18 Collections which can be grouped into 4 categories:

!!! Surveillance
    These collections contain the surveillance data sent by the [android application](./app/):

    - app_events
    - article_events
    - wifi_data
    - source_events

!!! Content
    These collections contain data which is used by the user. rss_source and categories are contain the recommended categories and sources, which will get suggested to the user. Privacy_policies and imprints contain the corresponding data which gets displayed to the user:

    - rss_source
    - categories
    - privacy_policis
    - imprints

!!! Usermanagement
    These collections are required and contain data about user management, registration and login system:

    - users
    - deleted_user_record
    - password_resets
    - oauth_access_tokens
    - oauth_auth_codes
    - auth_clients
    - oauth_personal
    - oauth_personal_access_clients

!!! Laravel
    These collections are required by the laravel framework from the [web backend](./web_backend/):

    - migrations
    - failed_jobs
