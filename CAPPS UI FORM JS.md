# Form.js Documentation

## Overview
The `form.js` file is used for any collection form in the application. It follows the structure:
```
<<APP NAME>>/rest/collection/<<Collection Name>>/form.js
```

The form.js file contains:
```javascript
capps.ui.<<collection_name>>.form = {
    // form handlers and methods
}
```

## Event Handlers
Form.js supports special event handlers that are called at specific points in the form lifecycle:

### _onLoadEvent
Called when the form is loaded/initialized.
```javascript
capps.ui.collection_name.form = {
    _onLoadEvent: async function (frm) {
        // Initialize form fields
        // Set default values
        // Load initial data
    }
}
```

### _onBeforeSave
Called before saving the form data. Can be used for validation, data transformation, and user confirmation.

**Important**: To properly interrupt the save operation, **throw an error** instead of returning `false`. The CAPPS framework awaits this function but doesn't check return values - only thrown errors will stop the save process.

```javascript
capps.ui.collection_name.form = {
    _onBeforeSave: async function (frm) {
        // Validate form data (read from current_form)
        if (!current_form.REQUIRED_FIELD) {
            await capps.ui.alert({
                title: "Validation Error",
                message: "Please fill required field",
                okVariant: "danger"
            });
            // Throw error to interrupt save - don't return false
            throw new Error("Required field validation failed");
        }
        
        // Show confirmation dialog
        const confirmed = await capps.ui.confirm({
            title: "Confirm Save",
            message: "Are you sure you want to save this record?",
            okTitle: "Save",
            cancelTitle: "Cancel"
        });
        
        if (!confirmed) {
            // User clicked Cancel - throw error to interrupt save
            throw new Error("Save operation cancelled by user");
        }
        
        // Transform data using form utilities (not current_form directly)
        if (current_form.CODE) {
            frm.set_value('CODE', current_form.CODE.toUpperCase());
        }
        
        // Return true to proceed with save
        return true;
    }
}
```

**Key Points**:
- **Use `async function`** for proper Promise handling
- **Throw errors to interrupt** the save operation
- **Use `await` with UI dialogs** (`capps.ui.alert`, `capps.ui.confirm`)  
- **Read data** from `current_form` (read-only global object)
- **Update data** using `frm.set_value()` form utilities, not `current_form` directly
- **Return `true`** to proceed with save (optional, but recommended for clarity)

### _afterFormSubmit
Called after the form is submitted. Can be used to perform operations after form submission, such as navigation, showing messages, or handling the result.

**Arguments:**
- `frm`: The form object.
- `{ result, resultHandler }`: An object containing the result of the form submission and a handler for the result.

**Usage:**
- Return `{ preventDefault: true }` to prevent the default post-submit behavior (such as navigation or automatic messages).
- You can use `capps.set_route()` to navigate programmatically after submission.

**Example:**
```javascript
capps.ui.collection_name.form = {
    async _afterFormSubmit(frm, { result, resultHandler }) {
        // Custom logic after form submit
        console.log(result);
        // Navigate to list view
        capps.set_route("view/list");
        // Prevent default behavior
        return {
            preventDefault: true
        };
    }
}
```

## Key Handlers
Form handlers are defined as functions that are called when field values change. The handler name matches the field name.

Example:
```javascript
capps.ui.collection_name.form = {
    FIELD_NAME: async function (frm) {
        // Handler logic here
    }
}
```

## Available Methods

### 1. set_value
```javascript
frm.set_value(fieldName: string, value: any)
```
- Sets the value of a form field
- Parameters:
  - fieldName: Name of the field
  - value: New value to set

### 2. clear_dropdown_options
```javascript
frm.clear_dropdown_options(fieldName: string)
```
- Clears all options from a dropdown field
- Parameters:
  - fieldName: Name of the dropdown field

### 3. set_dropdown_options
```javascript
frm.set_dropdown_options(fieldName: string, options: array)
```
- Sets options for a dropdown field
- Parameters:
  - fieldName: Name of the dropdown field
  - options: Array of options [{ label, value }]

### 4. set_field_enabled/set_field_disabled
```javascript
frm.set_field_enabled(fieldName: string)
frm.set_field_disabled(fieldName: string)
```
- Enables/disables a field
- Parameters:
  - fieldName: Name of the field

### 5. make_field_visible/make_field_invisible
```javascript
frm.make_field_visible(fieldName: string)
frm.make_field_invisible(fieldName: string)
```
- Makes a field visible/invisible
- Parameters:
  - fieldName: Name of the field

### 6. manage_validation_rule
```javascript
frm.manage_validation_rule(fieldName: string, validationRules: object)
```
- Updates validation rules for a field
- Parameters:
  - fieldName: Name of the field
  - validationRules: Object containing validation rules

### 7. set_query
```javascript
frm.set_query(fieldName: string, query: array)
```
- Sets a query for a field
- Parameters:
  - fieldName: Name of the field
  - query: Array of query filters

### 8. set_suggestions
```javascript
frm.set_suggestions(fieldName: string, suggestions: array)
```
- Sets suggestions for a field
- Parameters:
  - fieldName: Name of the field
  - suggestions: Array of suggestions

### 9. set_commands
```javascript
frm.set_commands(fieldName: string, commands: array)
```
- Sets commands for a field
- Parameters:
  - fieldName: Name of the field
  - commands: Array of commands

## Example Usage
```javascript
// Example form.js structure
capps.ui.collection_name.form = {
    // Event handlers
    _onLoadEvent: async function (frm) {
        // Initialize form
        frm.set_value("FIELD_NAME", "default value");
    },
    
    _onBeforeSave: async function (frm) {
        // Validate before save
        if (!frm.get_value("REQUIRED_FIELD")) {
            return false; // Prevent save
        }
    },

    // Field handler example
    FIELD_NAME: async function (frm) {
        // Set field value
        frm.set_value("FIELD_NAME", "new value");
        
        // Enable/disable field
        frm.set_field_enabled("FIELD_NAME");
        
        // Set dropdown options
        frm.set_dropdown_options("FIELD_NAME", [
            { label: "Option 1", value: "1" },
            { label: "Option 2", value: "2" }
        ]);
    }
}
```

---
[Go back to main page](../README.md)
