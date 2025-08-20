# Launcher App integration changes

1. Modularize the CSS of page-builder so that it can be used directly in CAPPs ui without any CSS glitches.
2. Remove iframe of page-builder from CAPPs Main page and use page-display (main page html) component directly there.

## Creation of launcher SSR page using page-builder

https://www.figma.com/proto/cqSDmsGDJOJsPcOOvrkduj/Launcher-Dashboard?page-id=0%3A1&node-id=1-2&viewport=217%2C262%2C0.07&t=DIA8SuLzOnfJnBQH-1&scaling=min-zoom&content-scaling=fixed

This has 3 parts

- main container
- dashboard list
- dashboard preview

## Main Container

- Script

    1. Get the dashboards data from `app_data_store` table with valid filters roles.
    2. Group all of available dashboards System (`CURRENT_DASHBOARD.JSONDATA.is_system === Y`), Others (`CURRENT_DASHBOARD.JSONDATA.is_system !== Y`);
    3. Query the `iwz_app_settings` table with APPID and USER_ID filters and store the app settings into the variable.
    4. To get the recently visited dashboard first filter out all available dashboards with its ID and recent dashboards ID , `CURRENT_DASHBOARD.ID === APP_SETTING_RECENT_DASHBOARD.ID` and then sort it these dashboards using `APP_SETTING_RECENT_DASHBOARD.lastAccess` key, top 5 0r 10 records can used for Recent.
    5. To get the Favorite dashboard filter out all available dashboards with its ID and favorite dashboard ID , `CURRENT_DASHBOARD.ID === APP_SETTING_FAVORITE_DASHBOARD.ID`.
    6. Default dashboard identifier.

        * Filter out the dashboard ID from APP_SETTINGS `APP_SETTING_FAVORITE_DASHBOARD.DEFAULT === true` and get the dashboard data.
        * If dashboard identifier has the any dashboard then `dashboard-preview` section should get opened by default with params `jsondata` and `ID`; else `dashboards-list` section.

- Template

    1. Create left panel and Dashboard list, Dashboard preview containers using this [page design](https://www.figma.com/proto/cqSDmsGDJOJsPcOOvrkduj/Launcher-Dashboard?page-id=0%3A1&node-id=1-2&viewport=217%2C262%2C0.07&t=DIA8SuLzOnfJnBQH-1&scaling=min-zoom&content-scaling=fixed)
    2. If the Default dashboard identifier has any value then open `dashboard-preview` section with params APP_ID and dashboard's ID, NAME, jsonData.
    3. If the default dashboard identifier does not have any value then open `dashboards-list` section by default with param as all dashboards data.

## Dashboard - List

- Section - Script

    1. Access the params and and store the all dashboards data in a variable.
    2. In params if FAVORITE flag is sent then update the favorite click activity in `iwz_app_settings` with APP_ID, and dashboard id.
    3. In params if DEFAULT flag is sent then update the default click activity in `iwz_app_settings` with APP_ID, and dashboard id by removing existing default setting.

- Section -  Template

    1. Display the all dashboards with its grouped type
    2. Dashboard will have the View, Set Default and Set Favorite action.
    3. On view action click, open the `dashboard-preview` section with params as APP_ID and dashboard's ID, NAME and its jsonData.
    4. On Favorite action call the rerender `dashboards-list` section with params with Favorite flag and APP_ID, dashboards ID to be marked as favorite
    5. On default action call the rerender `dashboards-list` section with params with default flag APP_ID, dashboards ID to be marked as favorite

## Dashboard - Preview

- Section - Script
    1. Update the recent activity in `iwz_app_settings` with APP_ID, and dashboard id
    2. Transform the params provided and use it in template to display the preview result of dashboard.

- Section - Template

    1. Display the preview based on dashboard type and jsondata
