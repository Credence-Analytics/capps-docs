# CAPPs - Adding Custom Actions to a Collection  

In many scenarios, standard CRUD operations provided by the CAPPs framework may not be sufficient. You may need custom workflows or process actions to enhance functionality. CAPPs allows you to define and integrate **custom actions** for specific collections easily.  

---

## 📌 **Defining a Custom Action**  

To define a custom action, create a `list.js` file inside the relevant collection’s directory.  

**Example Directory Structure:**  

```plaintext
.
└── credence/
    └── apps/
        └── my_awesome_app/
            └── rest/
                └── collection/
                    └── family/
                        ├── schema.json
                        └── list.js
```

Let’s take an example where we add an **"Authorize"** action to the `family` collection.  

**`list.js` Example:**  

```javascript
capps.ui.family.list = {
    AUTHORIZE: async function (list) {
        console.log("Selected records for authorization:", list.selectedRecords);
    }
}
```

---

## 🎯 **Using the Client-Side API for Customization**  

CAPPs provides a built-in client-side API that simplifies UI interactions. When a custom action is triggered, the handler function receives a `list` object containing attributes and methods for seamless UI customization.  

### ✅ **Available Attributes** - [🔗](https://docs.google.com/spreadsheets/d/1-WSgp326xEQUHYCLh24LPfXuoGsIzsaIpxrwzqUdhSQ/edit?gid=1593595884#gid=1593595884)

| Attribute        | Default Value | Description                                |
|-----------------|--------------|--------------------------------------------|
| `selectedRecords` | `[]`          | List of records selected from the grid   |

---

## 🏷️ **Updating Labels in `en.json` (Optional)**  

To ensure the **"Authorize"** action appears with a proper label in the UI, update `en.json` as follows:  

```json
{
    "my_awesome_app": {
        "family": {
            "headings": {},
            "fields": {},
            "actions": {
                "AUTHORIZE": "Authorize"
            }
        }
    }
}
```

---

## 📌 **Example: Custom Action with Bulkedit API Integration**  

The example below demonstrates how to use `capps.ui` API along with `capps.rest.bulkedit` API to execute custom bulk operations with scope-based validation.

### Basic Authorization Example

```javascript
capps.ui.family.list = {
    AUTHORIZE: async function (list) {
        const selectedRecords = list.selectedRecords;

        if (!selectedRecords.length) {
            return capps.ui.alert({
                message: "Please select at least one record to authorize.",
                variant: "danger",
            });
        }

        const confirm = await capps.ui.confirm({ 
            message: "Are you sure you want to authorize the selected records?" 
        });

        if (!confirm) return;

        const response = await capps.rest.credbooks.accounting_entries_mast.bulkedit({
            scope: "authorise",  // Scope defines the type of operation
            data: selectedRecords,
        });

        if (response?.status === "unsuccess") {
            return capps.ui.alert({
                message: response.error,
                variant: "danger",
            });
        }

        if (response?.status === "success") {
            return capps.ui.alert({
                message: response.msg || response.message || "Records authorized successfully",
                variant: "success",
            });
            
            // Refresh the list to show updated data
            list.refresh();
        }
    },
}
```

## 🔄 **Understanding Scope-Based Bulkedit Operations**

The `scope` parameter in the bulkedit API allows developers to define different validation rules and business logic for different types of bulk operations on the server side. This enables:

- **Multiple bulk operations** on the same collection with different validation rules
- **Conditional validation** based on the operation type
- **Server-side logic** specific to each scope
- **Different field requirements** per scope

### Multiple Scope Examples

Here are various examples showing different scopes for different business operations:

#### 1. Status Update Operations
```javascript
capps.ui.orders.list = {
    MARK_COMPLETED: async function (list) {
        const selectedRecords = list.selectedRecords;
        
        const response = await capps.rest.myapp.orders.bulkedit({
            scope: "mark_completed",
            data: selectedRecords.map(record => ({
                ID: record.ID,
                STATUS: "COMPLETED",
                COMPLETION_DATE: new Date().toISOString()
            }))
        });
        
        if (response?.status === "success") {
            capps.ui.alert({
                message: `${selectedRecords.length} orders marked as completed`,
                variant: "success"
            });
            list.refresh();
        }
    },
    
    CANCEL_ORDERS: async function (list) {
        const selectedRecords = list.selectedRecords;
        
        const response = await capps.rest.myapp.orders.bulkedit({
            scope: "cancel_orders", // Different scope, different validation
            data: selectedRecords.map(record => ({
                ID: record.ID,
                STATUS: "CANCELLED",
                CANCELLATION_REASON: "Bulk cancellation"
            }))
        });
        
        if (response?.status === "success") {
            capps.ui.alert({
                message: `${selectedRecords.length} orders cancelled`,
                variant: "success"
            });
            list.refresh();
        }
    }
}
```

#### 2. Approval Workflow Operations
```javascript
capps.ui.invoices.list = {
    BATCH_APPROVE: async function (list) {
        const selectedRecords = list.selectedRecords;
        
        const confirm = await capps.ui.confirm({
            message: `Approve ${selectedRecords.length} invoices?`
        });
        
        if (!confirm) return;
        
        const response = await capps.rest.financials.invoices.bulkedit({
            scope: "batch_approve",
            data: selectedRecords.map(record => ({
                ID: record.ID,
                APPROVAL_STATUS: "APPROVED",
                APPROVED_DATE: new Date().toISOString(),
                APPROVED_BY: "current_user" // This might come from session
            }))
        });
        
        if (response?.status === "success") {
            capps.ui.alert({
                message: `${selectedRecords.length} invoices approved successfully`,
                variant: "success"
            });
            list.refresh();
        } else {
            capps.ui.alert({
                message: response.error || "Approval failed",
                variant: "danger"
            });
        }
    },
    
    BATCH_REJECT: async function (list) {
        const selectedRecords = list.selectedRecords;
        
        // Get rejection reason from user
        const reason = await capps.ui.prompt({
            message: "Please provide rejection reason:",
            defaultValue: ""
        });
        
        if (!reason) return;
        
        const response = await capps.rest.financials.invoices.bulkedit({
            scope: "batch_reject", // Different scope for rejection
            data: selectedRecords.map(record => ({
                ID: record.ID,
                APPROVAL_STATUS: "REJECTED",
                REJECTION_REASON: reason,
                REJECTED_DATE: new Date().toISOString(),
                REJECTED_BY: "current_user"
            }))
        });
        
        if (response?.status === "success") {
            capps.ui.alert({
                message: `${selectedRecords.length} invoices rejected`,
                variant: "warning"
            });
            list.refresh();
        }
    }
}
```

#### 3. Assignment and Priority Operations
```javascript
capps.ui.tasks.list = {
    BULK_ASSIGN: async function (list) {
        const selectedRecords = list.selectedRecords;
        
        // Get assignment details from user
        const assignTo = await capps.ui.select({
            message: "Assign selected tasks to:",
            options: [
                { value: "team_lead", label: "Team Lead" },
                { value: "senior_dev", label: "Senior Developer" },
                { value: "junior_dev", label: "Junior Developer" }
            ]
        });
        
        if (!assignTo) return;
        
        const response = await capps.rest.project.tasks.bulkedit({
            scope: "bulk_assign",
            data: selectedRecords.map(record => ({
                ID: record.ID,
                ASSIGNED_TO: assignTo,
                ASSIGNMENT_DATE: new Date().toISOString(),
                STATUS: "ASSIGNED"
            }))
        });
        
        if (response?.status === "success") {
            capps.ui.alert({
                message: `${selectedRecords.length} tasks assigned to ${assignTo}`,
                variant: "success"
            });
            list.refresh();
        }
    },
    
    SET_PRIORITY: async function (list) {
        const selectedRecords = list.selectedRecords;
        
        const priority = await capps.ui.select({
            message: "Set priority for selected tasks:",
            options: [
                { value: "HIGH", label: "High Priority" },
                { value: "MEDIUM", label: "Medium Priority" },
                { value: "LOW", label: "Low Priority" }
            ]
        });
        
        if (!priority) return;
        
        const response = await capps.rest.project.tasks.bulkedit({
            scope: "set_priority", // Different scope for priority updates
            data: selectedRecords.map(record => ({
                ID: record.ID,
                PRIORITY: priority,
                PRIORITY_SET_DATE: new Date().toISOString()
            }))
        });
        
        if (response?.status === "success") {
            capps.ui.alert({
                message: `Priority set to ${priority} for ${selectedRecords.length} tasks`,
                variant: "success"
            });
            list.refresh();
        }
    }
}
```

## 🚨 **Advanced Error Handling with Scopes**

Different scopes may have different error scenarios. Here's how to handle scope-specific errors:

```javascript
capps.ui.employees.list = {
    BULK_SALARY_UPDATE: async function (list) {
        const selectedRecords = list.selectedRecords;
        
        const newSalary = await capps.ui.prompt({
            message: "Enter new salary amount:",
            type: "number"
        });
        
        if (!newSalary) return;
        
        const response = await capps.rest.hr.employees.bulkedit({
            scope: "salary_update", // This scope might have special validation
            data: selectedRecords.map(record => ({
                ID: record.ID,
                SALARY: parseFloat(newSalary),
                SALARY_EFFECTIVE_DATE: new Date().toISOString()
            }))
        });
        
        if (response?.status === "unsuccess") {
            // Handle scope-specific errors
            if (response.scope === "salary_update") {
                if (response.error?.includes("authorization")) {
                    capps.ui.alert({
                        message: "You don't have permission to update salaries",
                        variant: "danger"
                    });
                } else if (response.error?.includes("limit")) {
                    capps.ui.alert({
                        message: "Salary amount exceeds allowed limit",
                        variant: "warning"
                    });
                } else {
                    capps.ui.alert({
                        message: `Salary update failed: ${response.error}`,
                        variant: "danger"
                    });
                }
            }
            return;
        }
        
        if (response?.status === "success") {
            capps.ui.alert({
                message: `Salary updated for ${selectedRecords.length} employees`,
                variant: "success"
            });
            list.refresh();
        }
    }
}
```

## 📋 **Best Practices for Scope-Based Bulkedit**

1. **Use Descriptive Scope Names**: Choose clear names that indicate the operation type
   ```javascript
   // Good
   scope: "batch_approve"
   scope: "mark_shipped" 
   scope: "cancel_orders"
   
   // Avoid
   scope: "update1"
   scope: "operation"
   scope: "bulk"
   ```

2. **Handle Confirmation for Critical Operations**:
   ```javascript
   const confirm = await capps.ui.confirm({
       message: `Are you sure you want to delete ${selectedRecords.length} records? This cannot be undone.`
   });
   if (!confirm) return;
   ```

3. **Provide User Feedback**:
   ```javascript
   if (response?.status === "success") {
       capps.ui.alert({
           message: `${selectedRecords.length} records updated successfully`,
           variant: "success"
       });
       list.refresh(); // Always refresh the list after bulk operations
   }
   ```

4. **Document Your Scopes**: Keep track of what each scope does and its validation rules

## Further Reading

- [Detailed explanation of the UI API](./#%20CAPPS%20UI%20Framework.md) ?
- [Detailed explanation of the LIST API](./CAPPS%20UI%20LIST%20JS.md)
- [Detailed explanation of the FORM API](./CAPPS%20UI%20FORM%20JS.md)
- [Detailed explanation of the CARD API](./CAPPS%20UI%20CARD%20JS.md)
- [CAPPs Layout Documentation](./capps%20layout%20documentation.md#layout-structure)
- [CAPPs Custom Form Documentation](./capps_custom_add_modify_screen.md#virtual-collection-custom-addmodify-screen)

---
[Go back to main page](../README.md)