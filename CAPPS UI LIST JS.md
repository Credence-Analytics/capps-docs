# List.js Documentation

## Overview
The `list.js` file is used for configuring list/grid views and adding custom actions. It follows the structure:
```
<<APP NAME>>/rest/collection/<<Collection Name>>/list.js
```

The list.js file contains:
```javascript
capps.ui.<<collection_name>>.list = {
    // list handlers and configurations
}
```

## Features

### 1. Custom Actions
Define custom actions that can be triggered from the list view. Action names can be any valid string except reserved keywords.

Example:
```javascript
capps.ui.collection_name.list = {
    Approve: async function (list) {
        const selectedRecords = list.selectedRecords;
        // Action logic here
    },
    Reject: async function (list) {
        const selectedRecords = list.selectedRecords;
        // Action logic here
    }
}
```

### 1.a. Setting an Icon for a Custom Action (Approve/Reject etc.)
For custom actions such as Approve, Reject, etc. (i.e., actions defined as functions in your list.js), you can set an icon by targeting the action name in the list view using CSS.

> **Write these styles in:** `<<APP NAME>>/public/layout/style.css`

For example, to set an icon for the `Approve` action:

```css
li[data-action="Approve"] button::before {
    content: " ";
    background-image: url("./icons/Approve.svg");
    width: 22px;
    height: 22px;
    background-repeat: no-repeat;
    background-size: contain;
    background-position: center;
    display: inline-block;
    margin-right: 6px;
}
```

This will display the icon before the Approve button label in the list view. You can use the same approach for other custom actions by changing the `data-action` selector to match your action name (e.g., `Reject`, `Generate Bill`, etc.).

### 1.2 Custom actions using `buttonList`
The `buttonList` is a reserved key that accepts an array of action objects. This method provides more granular control over the button's appearance, visibility, and behavior compared to defining simple function-based actions. The order of buttons in the UI will match their order in the array.

Each object in the `buttonList` array can have the following properties:

| Property | Type | Required | Description |
| :--- | :--- | :--- | :--- |
| `key` | `String` | Yes | A unique identifier for the action. This is used internally and for `data-action` attributes. |
| `label` | `String` | No | The text displayed on the button. If not provided, the `key` is used as a fallback. |
| `icon` | `String` | No | A CSS class for an icon (e.g., `'pi pi-eye'`). The icon will be displayed before the label. |
| `show` | `Function` | No | A function that determines the button's visibility. It receives the `list` utility API object. It can return a boolean synchronously or a `Promise` that resolves to a boolean for asynchronous checks. If omitted, the button is always shown. |
| `handler`| `Function` | Yes | The function to execute when the button is clicked. It receives the `list` utility API object as an argument. |

Example:
```javascript
capps.ui.collection_name.list = {
    buttonList: [
        {
            key: 'Custom_Action',
            label: 'Custom Action',
            icon: 'pi pi-eye',
            // Synchronous show: button is always visible
            show: () => true,
            handler: (list) => { 
                alert("Calling modal action function from one of the action from buttonList");
                capps.ui.collection_one.list.customModalWithFieldsOption(list);
            }
        },
        {
            key: 'Custom_Action_2',
            label: 'Custom Action Two',
            icon: 'pi pi-eye',
            // Asynchronous show: button appears after 3 seconds
            show: async () => {
                return new Promise(function(resolve) {
                    setTimeout(function() {
                        resolve(true);
                    }, 3000);
                });
            },
            handler: (list) => { 
                alert("Calling modal action function from one of the action from buttonList");
            }
        },
        {
            key: 'Custom_Action_3',
            label: 'Custom Action Three',
            icon: 'pi pi-eye',
            // Asynchronous show: button is always hidden after 3 seconds
            show: async () => {
                return new Promise(function(resolve) {
                    setTimeout(function() {
                        resolve(false);
                    }, 3000);
                });
            },
            handler: (list) => { /* This handler will not be reachable */ }
        },
    ]
}
```


### 2. Column Sequence
Configure the order of columns in the grid view.

```javascript
capps.ui.collection_name.list = {
    columnSequence: [
        "ID",
        "NAME",
        "STATUS",
        "CREATED_DATE"
    ]
}
```

### 3. Column Formatting
Transform column values using custom formatters.

```javascript
capps.ui.collection_name.list = {
    formatters: {
        STATUS: function(value) {
            return value === "A" ? "Active" : "Inactive";
        },
        AMOUNT: function(value) {
            return new Intl.NumberFormat('en-US', {
                style: 'currency',
                currency: 'USD'
            }).format(value);
        }
    }
}
```

### 4. Row Styling
Modify row appearance based on conditions.

```javascript
capps.ui.collection_name.list = {
    rowRenderer: function(row) {
        if (row.STATUS === "EXPIRED") {
            return {
                style: {
                    backgroundColor: "#ffebee"
                }
            };
        }
        return {};
    }
}
```

### 5. Column Value Styling
Style individual column values dynamically.

```javascript
capps.ui.collection_name.list = {
    columnRowColorize: {
        STATUS: function(record) {
            if (record.STATUS === "ACTIVE") return {
                style: "color: blue!important",
            };
            if (record.STATUS === "INACTIVE") return {
                    style: "color: red!important",
            };
        }
    }
}
```

### 6. Before Render Hook
Execute code before the list data is rendered.

```javascript
capps.ui.collection_name.list = {
    before_render: async function(list) {
        // Pre-render logic here
    }
}
```

### 7. Button Columns
You can define multiple button columns using the reserved key `buttonColumns` in your `list.js` file. Each button will be displayed in a separate column.

**Example:**
```javascript
capps.ui.collection_name.list = {
    buttonColumns: [
        {
            key: 'View',
            label: 'View',
            icon: 'pi pi-eye',
            column_header: 'Details',
            show: (row) => true,
            handler: (row, context) => { /* view logic */ }
        },
        {
            key: 'Edit',
            label: 'Edit',
            icon: 'pi pi-pencil',
            column_header: '',
            show: (row) => row.status !== 'Closed',
            handler: (row, context) => { /* edit logic */ }
        },
        {
            key: 'Delete',
            label: 'Delete',
            icon: 'pi pi-trash',
            show: (row) => true,
            handler: (row, context) => { /* delete logic */ }
        }
    ],
    columnSequence: [
        "ID", "NAME", "STATUS", "View", "Edit", "Delete"
    ]
}
```
- Each button column can have its own label, icon, show logic, and handler.
- The `column_header` property (optional) controls the column header text for button columns. If not provided, the header will be blank.
- The `show` property is optional and controls visibility per row.
- The `handler` function receives the row and a context object.

## Handler Arguments

### List Handler Arguments
Each custom action and hook handler receives a `list` object with the following properties:

| Property | Type | Description |
|----------|------|-------------|
| selectedRecords | Array | List of selected records from the grid |
| clear_html_container | Function | Clears all custom HTML content |
| add_html | Function | Adds custom HTML content |
| remove_html | Function | Removes specific custom HTML content |

### Example Usage
```javascript
capps.ui.collection_name.list = {
    CustomAction: async function (list) {
        // Get selected records
        const selectedRecords = list.selectedRecords;
        
        // Add custom HTML
        list.add_html("customContainer", "<div>Custom Content</div>");
        
        // Remove custom HTML
        list.remove_html("customContainer");
        
        // Clear all custom HTML
        list.clear_html_container();
    }
}
```

## Reserved Keywords
The following keywords are reserved and should not be used as custom action names:
- columnSequence
- formatters
- rowRenderer
- columnRowColorize
- before_render
- buttonColumns
- buttonList

## Complete Example
```javascript
capps.ui.collection_name.list = {
    // Custom Actions
    Approve: async function (list) {
        const selectedRecords = list.selectedRecords;
        // Approval logic
    },


    buttonList: [
        {
            key: 'Custom_Action',
            label: 'Custom Action',
            icon: 'pi pi-eye',
            show: () => true,
            handler: (list) => { /* view logic */ }
        },
    ]


    // Button Columns
    buttonColumns: [
        {
            key: 'View',
            label: 'View',
            icon: 'pi pi-eye',
            column_header: 'Details',
            show: (row) => true,
            handler: (row, context) => { /* view logic */ }
        },
        {
            key: 'Edit',
            label: 'Edit',
            icon: 'pi pi-pencil',
            column_header: '',
            show: (row) => row.status !== 'Closed',
            handler: (row, context) => { /* edit logic */ }
        }
    ],
    
    // Column Configuration
    columnSequence: ["ID", "NAME", "STATUS", "View", "Edit"],
    
    // Column Formatting
    formatters: {
        STATUS: value => value === "A" ? "Active" : "Inactive"
    },
    
    // Row Styling
    rowRenderer: row => ({
        style: row.STATUS === "EXPIRED" ? { backgroundColor: "#ffebee" } : {}
    }),
    
    // Column Value Styling
    columnRowColorize: {
        STATUS(record) {
            return (record.STATUS !== "EXPIRED" && record.AMOUNT > 1000) 
                ? {
                    style: "color: blue!important",
                } : {}
        }
    },
    
    // Before Render Hook
    before_render: async function(list) {
        // Pre-render logic
    }
}
```

---
[Go back to main page](../README.md)
