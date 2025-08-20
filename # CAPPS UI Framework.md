# CAPPS Framework Documentation

## UI Methods

### 1. Alert Box
```javascript
capps.ui.alert({
    message: string,          // Required: Message to display
    size?: "sm" | "md" | "lg" | "xl",  // Optional: Size of alert box
    buttonSize?: "sm" | "md" | "lg",   // Optional: Size of buttons
    okVariant?: "success" | "danger" | "info" | "warning",  // Optional: Button style
    centered?: boolean,       // Optional: Center the alert box
    noCloseOnBackdrop?: boolean,  // Optional: Prevent closing on backdrop click
    title?: string           // Optional: Alert title
})
```
Shows an alert box and returns a promise that resolves when the alert is closed.

Example:
```javascript
await capps.ui.alert({
    title: "Success",
    message: "Record saved successfully!",
    size: "md",
    okVariant: "success"
});
```

### 2. Confirm Box
```javascript
capps.ui.confirm({
    title?: string,          // Optional: Confirmation title
    message: string,         // Required: Confirmation message
    size?: "sm" | "md" | "lg" | "xl",  // Optional: Size of confirm box
    buttonSize?: "sm" | "md" | "lg",   // Optional: Size of buttons
    okVariant?: "primary" | "success" | "danger" | "info" | "warning",  // Optional: Button style
    okTitle?: string,        // Optional: Confirm button text
    cancelTitle?: string,    // Optional: Cancel button text
    footerClass?: string,    // Optional: Custom CSS class for footer
    hideHeaderClose?: boolean  // Optional: Hide close button in header
})
```
Shows a confirmation box and returns a promise that resolves to:
- `true` if user clicks confirm
- `false` if user clicks cancel or closes the dialog

Example:
```javascript
const confirmed = await capps.ui.confirm({
    title: "Confirm Action",
    message: "Are you sure you want to delete this record?",
    okVariant: "danger",
    okTitle: "Delete",
    cancelTitle: "Cancel"
});

if (confirmed) {
    // Proceed with deletion
}
```

### 3. Toast Notification
```javascript
capps.ui.toast({
    title?: string,          // Optional: Toast title
    variant?: "success" | "danger" | "info" | "warning",  // Optional: Toast style
    message: string,         // Required: Toast message
    solid?: boolean,         // Optional: Solid background style
    autoHideDelay?: number   // Optional: Auto-hide delay in milliseconds
})
```
Displays a toast notification that automatically hides after the specified delay.

Example:
```javascript
capps.ui.toast({
    title: "Success",
    message: "Record saved successfully",
    variant: "success",
    autoHideDelay: 5000  // 5 seconds
});
```

### 4. Refresh List
```javascript
capps.ui.refresh()
```
Refreshes the current list collection by reloading data from the server.

Example:
```javascript
// After successful update
await capps.rest.credbooks.accounting_entries_mast.update["1"]({ data: { ... }});
capps.ui.refresh();  // Refresh the list to show updated data
```

### 5. Open Modal
```javascript
capps.ui.open_modal({
    title?: string,          // Optional: Modal title
    content: string,         // Required: HTML content or plain text
    size?: "sm" | "md" | "lg" | "xl",  // Optional: Modal size
    footer?: string,         // Optional: HTML content for footer
    style?: string,          // Optional: CSS styles for body content
    fields?: object[],       // Optional: Schema.json configuration for form
    handlers?: object[],     // Optional: Custom button handlers
    hideCloseButton?: boolean,  // Optional: Hide default close button (default: false)
    closeButtonLabel?: string   // Optional: Change close button text (default: "Close")
})
```
Opens a custom modal with specified content and styles. Returns a promise that resolves when the modal is closed.

Examples:

**Basic Modal:**
```javascript
await capps.ui.open_modal({
    title: "Information",
    content: "<p>This is a basic modal.</p>",
    size: "md"
});
```

**Modal with Custom Close Button Label:**
```javascript
await capps.ui.open_modal({
    title: "Confirmation",
    content: "<p>Are you sure you want to proceed?</p>",
    closeButtonLabel: "Cancel"
});
```

**Modal with Hidden Close Button:**
```javascript
await capps.ui.open_modal({
    title: "Loading",
    content: "<p>Please wait while we process your request...</p>",
    hideCloseButton: true
});
```

**Modal with Custom Handlers:**
```javascript
await capps.ui.open_modal({
    title: "Confirm Action",
    content: "<p>Do you want to save these changes?</p>",
    handlers: [
        {
            label: "Save",
            variant: "primary",
            handler: (ctx) => {
                // Save logic here
                ctx.closeModal();
            }
        },
        {
            label: "Cancel",
            variant: "secondary", 
            handler: (ctx) => ctx.closeModal()
        }
    ]
});
```

**Modal with Form Fields:**
```javascript
await capps.ui.open_modal({
    title: "Add New Record",
    content: "<div>Fill out the form below:</div>",
    size: "lg",
    fields: [
        {
            field: "name",
            label: "Name",
            type: "text",
            required: true
        },
        {
            field: "email",
            label: "Email",
            type: "email",
            required: true
        }
    ]
});
```

## REST APIs

### 1. Read Collection Data
```javascript
capps.rest.<<APP_NAME>>.<<COLLECTION_NAME>>.read({ 
    filter: Array<{
        field: string,      // Field name to filter on
        value: any,         // Value to filter by
        asgn: "eq" | "neq" | "gt" | "gte" | "lt" | "lte" | "in" | "nin",  // Assignment operator
        type?: string       // Optional: Data type for value comparison
    }>,
    sort?: Array<{
        field: string,      // Field to sort by
        order: "asc" | "desc"  // Sort order
    }>,
    limit?: number,         // Optional: Maximum number of records to return
})
```
Returns a promise that resolves to an array of records.

Example:
```javascript
const records = await capps.rest.credbooks.accounting_entries_mast.read({
    filter: [
        { field: "STATUS", value: "ACTIVE", asgn: "eq" },
        { field: "AMOUNT", value: 1000, asgn: "gt", type: "number" }
    ],
    sort: [
        { field: "CREATED_DATE", order: "desc" }
    ],
    limit: 10,
    offset: 0
});
```

### 2. Update Record
```javascript
capps.rest.<<APP_NAME>>.<<COLLECTION_NAME>>.update[<<ID>>]({ 
    data: {
        [field: string]: any  // Field-value pairs to update
    }
})
```
Returns a promise that resolves to the updated record.

Example:
```javascript
const updated = await capps.rest.credbooks.accounting_entries_mast.update["1"]({
    data: {
        STATUS: "INACTIVE",
        UPDATED_BY: "USER123"
    }
});
```

### 3. Create Record
```javascript
capps.rest.<<APP_NAME>>.<<COLLECTION_NAME>>.create({ 
    data: {
        [field: string]: any  // Field-value pairs for new record
    }
})
```
Returns a promise that resolves to the created record.

Example:
```javascript
const created = await capps.rest.credbooks.accounting_entries_mast.create({
    data: {
        NAME: "New Record",
        STATUS: "ACTIVE",
        CREATED_BY: "USER123"
    }
});
```

### 4. Delete Record
```javascript
capps.rest.<<APP_NAME>>.<<COLLECTION_NAME>>.delete[<<ID>>]()
```
Returns a promise that resolves when the record is deleted.

Example:
```javascript
await capps.rest.credbooks.accounting_entries_mast.delete["1"]();
```

## RPC (Remote Procedure Call)

```javascript
capps.rpc.<<APP_NAME>>.<<Plugin_Name>>.<<FUNCTION_NAME>>({ 
    payload: any  // Function-specific payload
})
```
Returns a promise that resolves to the function's result.

Example:
```javascript
const result = await capps.rpc.credbooks.FUNDS.isValidAmountExpression({
    amount: 1000,
    currency: "USD"
});
```

## Common Utilities

### 1. Route Utilities

#### a. Get Route (capps.get_route)
```javascript
capps.get_route()
```
Returns an array containing the current route information in the format: `[appName, docType, documentName, ...restPath]`

Example:
```javascript
const route = capps.get_route();
// Returns: ["credbooks", "doc", "accounting_entries_mast", "list"]
```

#### b. Set Route (capps.set_route)
```javascript
capps.set_route(route: string | string[], options?: {
    filter?: object,
    defaults?: object,
    [key: string]: any
})
```
Navigates to a specified route with optional query parameters.

Parameters:
- `route`: String or array representing the route path
- `options`: Optional object containing:
  - `filter`: Object for filtering data
  - `defaults`: Object for default values
  - Any additional query parameters

Example:
```javascript
// Navigate to list view with filter
capps.set_route("list", {
    filter: { STATUS: "ACTIVE" },
    defaults: { TYPE: "SALE" }
});

// Navigate to specific document
capps.set_route(["doc", "accounting_entries_mast", "form", "123"]);
```

### 2. Format Utility (capps.format)
```javascript
capps.format(value: any, options?: {
    fieldtype?: 'Date' | 'Datetime' | 'Currency' | 'Int' | 'Float',
    format?: string,
    inputFormat?: string,
    currency?: string,
    precision?: number
})
```
Formats a value based on the specified field type and options.

Parameters:
- `value`: Value to format
- `options`: Formatting options:
  - `fieldtype`: Type of field ('Date', 'Datetime', 'Currency', 'Int', 'Float')
  - `format`: Output format (for dates)
  - `inputFormat`: Input format (for dates)
  - `currency`: Currency symbol (for currency)
  - `precision`: Number of decimal places (for numbers)

Example:
```javascript
// Format date
capps.format("2024-03-25", {
    fieldtype: "Date",
    format: "DD/MM/YYYY"
}); // Returns: "25/03/2024"

// Format currency
capps.format(1234.56, {
    fieldtype: "Currency",
    currency: "$"
}); // Returns: "$1,234.56"

// Format number
capps.format(1234.5678, {
    fieldtype: "Float",
    precision: 2
}); // Returns: "1,234.57"
```

### 3. Require Utility (capps.require)
```javascript
capps.require(assets: string | string[], callback?: function)
```
Loads external assets (JS, CSS, HTML) and executes a callback when complete.

Parameters:
- `assets`: Single asset URL or array of asset URLs
- `callback`: Optional function to execute after assets are loaded

Supported asset types:
- `.js`: JavaScript files
- `.css`: Stylesheet files
- `.html`/`.htm`: HTML files (including embedded scripts and stylesheets)

Example:
```javascript
// Load single asset
capps.require("/path/to/script.js", (result) => {
    console.log("Script loaded");
});

// Load multiple assets
capps.require([
    "/path/to/script.js",
    "/path/to/style.css",
    "/path/to/template.html"
], (results) => {
    console.log("All assets loaded");
});

// Load HTML with embedded scripts and styles
capps.require("/path/to/template.html", (htmlContent) => {
    // htmlContent contains the HTML with scripts and styles removed
    // as they are loaded separately
});
```

Note: The require utility:
- Handles relative and absolute URLs
- Loads scripts asynchronously
- Processes embedded scripts and styles in HTML files
- Returns a Promise that resolves when all assets are loaded
- Automatically resolves relative paths in HTML files
- Handles errors for failed asset loading

## Collection Access

### Access Collection Details

```javascript
capps.ui.collection
```

Provides access to the current collection's metadata.

#### Properties

- **collectionName**  
  The internal name of the collection (e.g., "bill").

- **schema**  
  An object describing the collection schema, with the following keys:
  - `ACTIONS`: Array of allowed actions (e.g., `["CREATE", "DELETE", "EXPORT"]`)
  - `DATA_MODEL`: Backend data model name (e.g., `"CA_BOOK_MASTER"`)
  - `FIELDS`: Object containing all field definitions
  - `NAME`: Human-readable collection name (e.g., `"Books"`)
  - `VIRTUAL_COLLECTION`: (if present) Indicates if this is a virtual collection
  - ... (any other keys present in the schema)

#### Example

```javascript
// Get collection name
const name = capps.ui.collection.collectionName;

// Get schema object
const schema = capps.ui.collection.schema;

// Get all actions allowed on this collection
const actions = schema.ACTIONS;

// Get all field definitions
const fields = schema.FIELDS;

// Get the backend data model name
const dataModel = schema.DATA_MODEL;

// Get the human-readable collection name
const displayName = schema.NAME;
```