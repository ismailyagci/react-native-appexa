# react-native-appexa Documentation

Welcome to the documentation for `react-native-appexa`. This library provides foundational tools and structures for building React Native applications, focusing on state management with Redux, API request handling, and data validation.

## Core Concepts

-   **Provider:** The main entry point (`<Appexa>`) that initializes the library, Redux store, and configuration for your React Native app.
-   **Store:** A Redux-based state management system with helpers for creating modular stores, especially for CRUD operations.
-   **Request:** A configuration-driven module for making API requests using Axios, integrated with `appexa.json`.
-   **Validation:** A utility for validating data against a schema, useful for forms and API inputs.
-   **Configuration (`appexa.json`):** A central JSON file defining API endpoints and base URLs.

## Documentation Index

**Getting Started & Overview:**

-   [README](../README.md): Main project overview, installation, and basic usage.

**Core Modules:**

-   [Provider Component](./provider.md): Documentation for the main `<Appexa>` provider, which sets up the context for your React Native application.
-   [Request Module](./request.md): How API requests are configured and made.
-   [Validation Module](./validation.md): How to use the data validation features.
    -   [Validation Types](./validation-types.md): Details on each specific validation rule implementation.
    -   [Validation Messages](./validation-messages.md): Default error messages for validation.
    -   [Validation Utility](./validation-utils.md): Internal helper for validation.

**State Management (Store):**

-   [Store Setup](./store.md): Core concepts of the Redux store integration.
-   [Store Module Example: Auth](./storeModules.md): Detailed example of an authentication module for React Native, using AsyncStorage.
-   [Using the CRUD Class](./storeModulesCrud.md): How to simplify CRUD state management, with a React Native `FlatList` example.
-   [`store.CRUD` Base Class](./crudClass.md): Technical details of the CRUD helper class.
-   [Async Dispatch Utilities](./asyncDispatch.md): Using `asyncDispatch` and `withForward` for sequential actions.

**Internal & Utility Modules:**

-   [Internal Container](./container.md): The internal mechanism for sharing config/state within the library.
-   [Utility Functions](./utils.md): General helper functions (`toCamelCase`, `isObject`, etc.).
-   [Services Module](./services.md): Placeholder for potential service abstractions.
