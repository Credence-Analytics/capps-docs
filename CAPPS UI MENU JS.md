# CAPPS Menu JS Documentation

## Overview

`menu.js` files define the navigation structure for a module or application in the capps framework. These files are loaded at runtime and can be used to control the navigation menus.

## File Format

- The file must use CommonJS module syntax:
  ```js
  module.exports = [ ... ];
  ```
- You may also use ES module default export:
  ```js
  export default [ ... ];
  ```
  (Both are supported if loaded with `capps.require(..., { asModule: true })`)

## Menu Structure

A menu file exports an array of menu item objects. Each object can represent a menu item or a group with sub-items.

### Example
```js
module.exports = [
  {
    label: "Home",
    icon: "pi pi-home",
    route: "myapp/pages/home"
  },
  {
    label: "Administration",
    icon: "pi pi-user",
    items: [
      {
        label: "Users",
        icon: "pi pi-users",
        route: "myapp/doc/users/view/list"
      },
      {
        label: "Settings",
        icon: "pi pi-cog",
        route: "myapp/doc/settings/view/list"
      }
    ]
  }
];
```

### Menu Item Properties
- `label` (string): Display name for the menu item.
- `icon` (string, optional): Icon class for the menu item.
- `route` (string, optional): Route path to navigate to.
- `items` (array, optional): Array of sub-menu items.
- `hidden` (boolean, optional): If true, hides the item from the menu.

## Debugging

Menu files loaded as modules are debuggable in browser devtools (Sources panel) due to the use of `//# sourceURL`.

## Best Practices

- Keep menu definitions simple and declarative.
- Avoid importing other modules or using build-time dependencies in menu.js.
