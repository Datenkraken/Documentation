# Changing displayed project name

To change the displayed project name, you will just need to adjust some strings in both the Backend and the App.

## App

Go to `app/src/main/res/values/strings.xml` and `values-de/strings.xml` and change the string `app-name` to the desired name. 
You might also want to change `app_subtitle` to change the subtitle of the app displayed in the navigation menu.

## Backend

To change the display name of the dashboard, simply change the `APP_NAME` in your `.env` file, on the deployed server.
This changes the title of every dashboard page and the text in the topbar.
