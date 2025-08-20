# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

CAPPS (Credence Application Platform and Services) is a Vue.js 2 framework for building financial applications. This is the UI component of a larger microservices-based platform that includes REST services, event consumers, and page builders.

## Development Commands

### Core Development
- **Development server**: `npm run serve` (runs on port 8888)
- **Build**: `npm run build`
- **Tests**: `npm run test` (Jest unit tests)
- **JSON Server** (mock data): `npm run jsonserver` (port 50001)

### Vue3 MFE (Micro-Frontend)
- **Vue3 MFE Development**: `cd packages/vue3-mfe && npm run serve` (runs on port 7979 with HTTPS)
- **Vue3 MFE Build**: `cd packages/vue3-mfe && npm run build` (outputs to `dist__vue3-mfe/`)
- **Vue3 MFE Lint**: `cd packages/vue3-mfe && npm run lint`

### Configuration
- **App configuration**: `npm run appconfig` - Manages application configuration setup

## Architecture Overview

### Core Framework Structure
- **Framework Core**: `src/Framework/capps.js` - Main framework entry point exposing REST, RPC, UI, and utility APIs
- **Bootstrap**: `src/main.js` → `src/bootstrap.js` - Application initialization with config loading
- **Configuration**: Dynamic config loading from `config.js` with product-specific overrides

### Key Architectural Components

#### 1. Module Federation Setup
- Uses webpack module federation for micro-frontends
- Remote applications configured in `micro-frontend-configs/`
- Supports loading external components (app-components, page-builder)
- **Vue3 MFE Integration**: `packages/vue3-mfe/` - Local Vue 3 micro-frontend with PrimeVue components

#### 2. Framework API Structure (`src/Framework/`)
- **REST API** (`JS API/REST/`) - HTTP request handling
- **RPC API** (`JS API/RPC/`) - Remote procedure calls  
- **UI API** (`JS API/UI/`) - UI utilities and components
- **Common Utilities** - Shared utility functions

#### 3. CAPPS Module System (`src/modules/CAPPS/`)
- **CollectionScreen.vue** - Main data collection interface
- **FormScreen.vue** - Form rendering and handling
- **ListView.vue** - List/table data presentation
- **Block components** - ModuleBlock.vue, CollectionBlock.vue for layout

#### 4. Form System (`src/modules/FormComponents/`)
- Dynamic form field rendering with validation
- Field types: TextField, SelectField, DateField, NumberField, etc.
- Form element mapping and custom validation rules

#### 5. Data Visualization (`src/components/DataVisualisation/`)
- TableView and CardView components
- Pagination and filtering capabilities
- Chart integration (ApexCharts, Chart.js, Plotly)

### State Management (Vuex)
- **Store modules** in `src/store/modules/`
- Key stores: `collectionBlock.store.js`, `moduleBlock.store.js`, `cappsPageConfigurations.store.js`

### Testing Setup
- Jest configuration in `jest.config.js`
- Unit tests in `src/**/*.tests.js`
- Mock setup in `__tests__/__mocks__/`

## Key Development Patterns

### Configuration Management
- Global config object loaded dynamically at startup
- Product-specific configurations (funds, mercury, etc.) in `public/config.*.example.js`
- Environment-specific settings for NREST/JREST endpoints

### Import Aliases and Module Resolution
- **`@/`** - Standard Vue CLI alias pointing to `src/` directory (e.g., `import store from "@/store"`)
- **`config.js`** - Custom alias pointing to `./config/` directory (resolved via webpack alias)
- **External modules**: `Plotly`, `config`, `capps` are configured as webpack externals
- **Config imports**: Use `from "config.js"` to import dynamic configuration loader
- **Config access**: Runtime config available as `from "config"` for micro-frontend configurations

### Component Architecture
- Vue 2 with Bootstrap-Vue for UI components
- Mixin-based functionality in `src/mixins/`
- Custom directives in `src/directives/`

### API Integration
- REST calls through `credCAPI` utility (`src/utils/credCAPI/`)
- Server-side pagination and filtering
- Event-driven notifications system

### Schema-Driven Development
- JSON schema definitions drive form and list rendering
- Schema files in `json_server/capps/` for development
- Dynamic field validation and UI generation

## Important File Locations

### Configuration Files
- `vue.config.js` - Webpack and dev server configuration with custom aliases
- `public/config.js` - Runtime configuration loader
- `config/index.js` - Configuration loader with error handling and overlay
- `config/applicationDetails.js` - App-specific settings

### Core Framework Files
- `src/Framework/capps.js` - Main framework API exports
- `src/bootstrap.js` - Application bootstrapping
- `src/ourVue.js` - Vue instance configuration

### Key Components
- `src/App.vue` - Root application component
- `src/modules/CAPPS/CollectionScreen.vue` - Primary data collection interface
- `src/components/layout/layout.vue` - Dashboard layout system

## Development Guidelines

### When Working with Forms
- Form fields are dynamically generated from schema configurations
- Custom field types should extend base field classes in `src/modules/FormComponents/formFields/`
- Validation rules are defined in `src/utils/veeValidateRules/`

### When Working with Collections
- Use existing collection utilities in `src/modules/CollectionStateUtilities/`
- List rendering follows the ListJS pattern with configurable formatters
- Filter and search functionality is schema-driven

### When Adding New Features
- Follow the existing modular structure under `src/modules/`
- Use the centralized store pattern for state management
- Integrate with the existing notification system via `src/plugins/Notifications/`

### Micro-Frontend Integration
- Remote components loaded via webpack module federation
- Configuration in `micro-frontend-configs/remotes.js`
- Lazy loading for performance optimization

### Vue3 Component Integration (Local Mono-repo)
This project includes a local Vue 3 micro-frontend in `packages/vue3-mfe/` that provides modern Vue 3 components to the Vue 2 application.

#### Architecture:
- **Location**: `packages/vue3-mfe/` - Self-contained Vue 3 application with PrimeVue UI library
- **Build Output**: Components are built to `dist__vue3-mfe/` directory
- **Module Federation**: Exposes Vue 3 components via webpack module federation as `capps_vue3_package`
- **Integration**: Loaded in Vue 2 app via `Vue3ComponentLoader` component

#### Available Vue3 Components:
- `MyVue3Component` - Basic Vue 3 component example
- `DataTable` - PrimeVue-based data table component  
- `MenuBar` - PrimeVue menu bar component
- `RecordSummaryDetails` - Record summary component for financial data

#### How to Use Vue3 Components in Vue2 App:
```vue
<Vue3ComponentLoader 
  :exposed-module="'./DataTable'"
  :component-props="{ data: tableData, columns: columns }"
  :component-events="{ rowSelect: handleRowSelect }"
/>
```

#### Key Files:
- `packages/vue3-mfe/vue.config.js` - Module federation configuration
- `packages/vue3-mfe/src/exposes/` - Entry points for exposed components
- `packages/vue3-mfe/src/utils/createMfeInterface.js` - Vue 3 component wrapper utility
- `src/components/Vue3ComponentLoader/Vue3ComponentLoader.vue` - Vue 2 loader component
- Configuration: `remoteApp3` in `public/config.*.example.js` files

#### Development Workflow:
1. Develop Vue 3 components in `packages/vue3-mfe/src/components/`
2. Create expose entry in `packages/vue3-mfe/src/exposes/`
3. Add to module federation exposes in `packages/vue3-mfe/vue.config.js`  
4. Build Vue3 MFE: `cd packages/vue3-mfe && npm run build`
5. Use in Vue 2 app via `Vue3ComponentLoader`