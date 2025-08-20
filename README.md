# CAPPS Framework Documentation

**CAPPS** is a framework for building schema-driven financial applications with powerful form generation, data management, and workflow capabilities.

## 🚀 Quick Start

- **New to CAPPS?** Start with [Installation Guide](./docs/INSTALLATION.md#installation)
- **Want to build your first app?** Follow [How to create a new App](./docs/How%20to%20create%20a%20new%20App.md#how-to-create-a-new-app)
- **Need complete reference?** Check [Schema Configuration Reference](./docs/CAPPS%20Schema%20Configuration%20Reference.md)

## 📚 Documentation Index

### **Getting Started**
1. [📦 Installation](./docs/INSTALLATION.md#installation) - Setup and installation guide
2. [🆕 How to create a new App](./docs/How%20to%20create%20a%20new%20App.md#how-to-create-a-new-app) - Step-by-step app creation
3. [🏗️ CAPPS UI Framework](./docs/CAPPS%20UI%20Framework.md#capps-ui-framework) - Core framework overview

### **Schema & Configuration**
4. [⚙️ Schema Configuration Reference](./docs/CAPPS%20Schema%20Configuration%20Reference.md) - Complete field types and properties reference
5. [📋 Schema Configurations](./docs/CAPPS%20Schema%20configurations.md) - Schema setup guide

### **Field Types & Properties**
6. [🔧 Advanced Field Types](./docs/CAPPS%20Advanced%20Field%20Types.md) - Specialized field configurations
7. [📝 Advanced Field Properties](./docs/CAPPS%20Advanced%20Field%20Properties.md) - Field property details

### **Frontend Development**
8. [📄 CAPPS UI FORM JS](./docs/CAPPS%20UI%20FORM%20JS.md#formjs-documentation) - Form event handling and customization
9. [📊 CAPPS UI LIST JS](./docs/CAPPS%20UI%20LIST%20JS.md#listjs-documentation) - List view customization
10. [🃏 CAPPS UI CARD JS](./docs/CAPPS%20UI%20CARD%20JS.md) - Card view templates
11. [🧭 CAPPS UI MENU JS](./docs/CAPPS%20UI%20MENU%20JS.md) - Menu and navigation
12. [🎨 Layout Documentation](./docs/capps%20layout%20documentation.md#layout-structure) - UI layout and styling

### **Backend Development**
13. [🔌 CAPPS APIs](./docs/CAPPS%20APIS.md#capps-apis) - User-defined actions and API integration
14. [🌐 REST API Reference](./docs/CAPPS%20REST%20API%20Reference.md) - Complete CRUD API documentation with examples
15. [🔗 Event Hooks System](./docs/CAPPS%20Event%20Hooks%20System.md) - Background processing and workflows
16. [🧩 Plugin System](./docs/CAPPS%20Plugin%20System.md) - RPC plugins and business logic
17. [📈 Process Progress System](./docs/CAPPS%20Process%20Progress%20System.md) - Real-time progress tracking

### **Advanced Features**
18. [📁 File Handling & Bulk Operations](./docs/CAPPS%20File%20Handling%20and%20Bulk%20Operations.md) - File uploads and bulk processing
19. [🖥️ Custom Form Documentation](./docs/capps_custom_add_modify_screen.md#virtual-collection-custom-addmodify-screen) - Custom form screens

### **Database & Setup**
20. [🗄️ Database Schema Guide](./docs/CAPPS%20Database%20Schema%20Guide.md) - Database setup, field mapping, and migration guide
21. [⚡ Framework Collections](./docs/CAPPS%20Framework%20Collections.md) - Required system collections and their usage

### **Complete Reference**
22. [📖 CAPPS Master Knowledge Base](./docs/CAPPS-MASTER-KNOWLEDGE-BASE.md) - Comprehensive framework reference (consolidated guide)

---

## 🏗️ Framework Architecture

CAPPS follows a **schema-driven development** approach where:

- **JSON schemas** define your entire application structure
- **CAPPs UI engine** render forms, lists, and cards automatically
- **Event hooks** handle background processing
- **Plugins** provide custom business logic
- **APIs** integrate with external systems

## 📖 Key Concepts

| Concept | Description | Documentation |
|---------|-------------|---------------|
| **Collections** | Data structures defined by JSON schemas | [Schema Reference](./docs/CAPPS%20Schema%20Configuration%20Reference.md) |
| **Field Types** | 20+ field types (text, select, date, file, etc.) | [Advanced Field Types](./docs/CAPPS%20Advanced%20Field%20Types.md) |
| **Form Events** | JavaScript handlers for form interactions | [Form JS](./docs/CAPPS%20UI%20FORM%20JS.md) |
| **Event Hooks** | Background processing triggers | [Event Hooks](./docs/CAPPS%20Event%20Hooks%20System.md) |
| **Plugins** | Custom RPC business logic | [Plugin System](./docs/CAPPS%20Plugin%20System.md) |

## 🔧 Development Workflow

1. **Define Schema** → Create JSON configuration for your collection
2. **Generate Forms** → Framework auto-generates UI components  
3. **Add Logic** → Implement form events and validation
4. **Process Data** → Use event hooks for background tasks
5. **Deploy** → Your application is ready!

---

## 💡 Need Help?

- **Quick Reference**: [Schema Configuration Reference](./docs/CAPPS%20Schema%20Configuration%20Reference.md)
- **Examples**: Check the [CAPPs Guinea Pig](https://gitlab.credenceanalytics.com/apps/capps-guinea-pig) demo application

---
