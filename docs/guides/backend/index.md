# Overview

## Introduction

The backend's main functions are data analysis, authentification and the display of the analyzed data. But it is also used to adjust settings of the current iteration, the data policy, the imprint and other options.

This page explains the features of the dashboard from a users perspective to give an overview.

## Dashboard Features And Functions

### Home

The Home tab is the first page that opens after logging into the dashboard. Here you can find some graphs and statistics about the current iteration.

### User Management

This tab is used for managing users. Admins can delete single and multiple users. This is especially helpful when users request the deletion of their account. When an account is deleted, all data that is linked to that account is also deleted.

### Sources

In this tab administators can manage sources that are displayed to the users when they first start the app.
The sources should point to a valid rss feed. Each source can be have multiple catagories, which can be assigned when editing or creating a source.

### Privacy Policy and Imprint

These functions allow the user to edit the imprint and the data policy. One should be carfull though, since all edits are instantly published after saving them. All available features of the WYSIWYG-Editor can be found in its [documentation](https://ckeditor.com/docs/ckeditor5/latest/features/index.html).

### OAuth

Usually this is only needed for testing / development purposes, but you may also need this to create a new public OAuth Client (without the confidential flag and a redirect to `datenkrake://oauthresponse`) for the app, so that the app can redirect users to the login and access the API.
With a client, you can copy the Client ID and put it in the `keys.xml` file within the app, before building the APK that is distributed.
You can also create Personal Access Tokens, that can be used to authenticate against the API instead of first obtaining a valid access token via an OAuth Flow.
Read more about OAuth and it's responsiblities [here](https://oauth.net/2/).

### Retention

This tab can be used to control the automatic deletion of users and their data.
You can decide between a per User limited lifetime (e.g. a User account should only last 2 weeks and is deleted after that) or a global date limit, that will remove all accounts after the configured date and additionally will close the registration (When the executed indicator is green).