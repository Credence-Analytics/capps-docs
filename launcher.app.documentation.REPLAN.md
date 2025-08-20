# Launcher App integration changes

1. Modularize the CSS of page-builder so that it can be used directly in CAPPs ui without any CSS glitches.
2. Remove iframe of page-builder from CAPPs Main page and use page-display (main page html) component directly there.

## Creation of launcher SSR page using page-builder

https://www.figma.com/proto/cqSDmsGDJOJsPcOOvrkduj/Launcher-Dashboard?page-id=0%3A1&node-id=1-2&viewport=217%2C262%2C0.07&t=DIA8SuLzOnfJnBQH-1&scaling=min-zoom&content-scaling=fixed

This has 3 parts

- main container
- dashboard list
- dashboard preview

## CAPPs dashboard launcher pages

1. Create `/launch/` route in capps.
2. For this route create new launcher component
3. Create collapsible panel and preview container layout in launcher component
4. Add search, Starred and System dashboards at left side of collapsible panel of launcher component
5. Load all of the dashboards using `/pages/read/` API
6. Create preview component which will display the preview of selected component.
7.  
