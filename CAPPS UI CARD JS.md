# Card.js Documentation

## Overview
The `card.js` file is used for configuring the summary card view for a collection. It displays fields as defined in the corresponding `schema.json` file for that collection, allowing you to present key details (such as voucher, book, policy, event, asset class, portfolio, etc.) in a card format.

The `template` key is used to define the card template in `card.js`.

> **Note:** If a `card.js` file is present for a collection, the card layout will be available for access. If not, the standard framework list view will be used instead.

The typical location for a card.js file is:
```
<<APP NAME>>/rest/collection/<<Collection Name>>/card.js
```

The card.js file contains:
```javascript
capps.ui.<<collection_name>>.card = {
    template: `...`, // HTML template for the card
}
```

## Features

### 1. Card Template
Defines the HTML structure for displaying record details in a card format. Uses mustache-style bindings (e.g., `{{ doc.FIELD_NAME }}`) to display data.

**Example:**
```javascript
capps.ui.accounting_entries_mast.card = {
    template: `
        <div class="card">
            <div class="card-header">
                <div>
                    <h4 class="text-capitalize">{{ doc.VOUCHER_NAME }} {{ doc.VOUCHER_NO ? '- ' + doc.VOUCHER_NO : '' }}</h4>
                    <div class="icon_wrap date" style="grid-template-columns: 1fr;"><span>{{doc.VALUEDATE}}</span></div>
                </div>
                <div class="text-truncate">
                    <div class="text-right badge badge-info badge-pill text-truncate" style="max-width: 130px" ><span style="color: white;">{{ doc.DEAL_NO }}</span></div>
                </div>
            </div>
            <div class="card-body">
                <div class="card-content grid--3">
                    <div class="content">
                        <div class="hd">BOOK</div>
                        <div class="cont text-truncate"> {{ doc.BOOK }} </div>
                    </div>
                    <div class="content">
                        <div class="hd">POLICY</div>
                        <div class="cont text-truncate">{{ doc.POLICY }}</div>
                    </div>
                    <div class="content">
                        <div class="hd">EVENT NAME</div>
                        <div class="cont text-truncate">{{ doc.EVENT_NAME }}</div>
                    </div>
                    <div class="content">
                        <div class="hd">ASSET CLASS CODE</div>
                        <div class="cont text-truncate">{{ doc.ASSET_CLASS_CODE }}</div>
                    </div>
                    <div class="content">
                        <div class="hd">PORTCODE</div>
                        <div class="cont text-truncate">{{ doc.PORTCODE }}</div>
                    </div>
                    <div class="content">
                        <div class="hd">PORTFOLIO</div>
                        <div class="cont text-truncate">{{ doc.PORTFOLIO }}</div>
                    </div>
                </div>
            </div>
        </div>
    `,
}
```

### 2. Data Binding
The template uses the `doc` object to bind and display field values. All placeholders like `{{ doc.FIELD_NAME }}` are dynamically replaced with the corresponding values from the record.

> **Note:** The `doc` object contains the current row's data. Each time a card is rendered, `doc` will have the data for that specific record.

### 3. Customization
You can customize the card layout, add or remove fields, and style the card as needed by editing the HTML template string.

## Usage

- The card component is dynamically loaded by the capps framework.
- It is typically used to show a summary view for a record in a collection.
- The template is injected and rendered using the framework's dynamic component loader.

**Integration Example:**
```javascript
// This card will be loaded for the accounting_entries_mast collection
capps.ui.accounting_entries_mast.card = {
    template: `...`, // See above for full template
}
```

## Complete Example
```javascript
capps.ui.accounting_entries_mast.card = {
    template: `
        <div class="card">
            <div class="card-header">
                <div>
                    <h4 class="text-capitalize">{{ doc.VOUCHER_NAME }} {{ doc.VOUCHER_NO ? '- ' + doc.VOUCHER_NO : '' }}</h4>
                    <div class="icon_wrap date" style="grid-template-columns: 1fr;"><span>{{doc.VALUEDATE}}</span></div>
                </div>
                <div class="text-truncate">
                    <div class="text-right badge badge-info badge-pill text-truncate" style="max-width: 130px" ><span style="color: white;">{{ doc.DEAL_NO }}</span></div>
                </div>
            </div>
            <div class="card-body">
                <div class="card-content grid--3">
                    <div class="content">
                        <div class="hd">BOOK</div>
                        <div class="cont text-truncate"> {{ doc.BOOK }} </div>
                    </div>
                    <div class="content">
                        <div class="hd">POLICY</div>
                        <div class="cont text-truncate">{{ doc.POLICY }}</div>
                    </div>
                    <div class="content">
                        <div class="hd">EVENT NAME</div>
                        <div class="cont text-truncate">{{ doc.EVENT_NAME }}</div>
                    </div>
                    <div class="content">
                        <div class="hd">ASSET CLASS CODE</div>
                        <div class="cont text-truncate">{{ doc.ASSET_CLASS_CODE }}</div>
                    </div>
                    <div class="content">
                        <div class="hd">PORTCODE</div>
                        <div class="cont text-truncate">{{ doc.PORTCODE }}</div>
                    </div>
                    <div class="content">
                        <div class="hd">PORTFOLIO</div>
                        <div class="cont text-truncate">{{ doc.PORTFOLIO }}</div>
                    </div>
                </div>
            </div>
        </div>
    `,
}
```

---
[Go back to main page](../README.md)