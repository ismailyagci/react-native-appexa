# Store Module (`/src/store/index.js`)

This module is the heart of the state management system in `react-native-appexa`, built upon Redux. It provides the necessary components and functions to create, configure, and interact with the Redux store.

## Core Components & Exports

-   **`StoreProvider`**: A React component that wraps the application, initializes the Redux store, and makes it available via `react-redux`'s `Provider`.
-   **`createStoreModules` (internal)**: A function responsible for creating the Redux store instance, combining reducers from provided modules, and applying middleware.
-   **`getStore`**: A utility function to get a direct reference to the created Redux store instance (use with caution, prefer hooks/connect).
-   **`useDispatch`**: Re-export of `react-redux`'s `useDispatch` hook for dispatching actions.
-   **`useSelector`**: Re-export of `react-redux`'s `useSelector` hook for selecting state from the store.
-   **`asyncDispatch`**: A utility function for dispatching multiple asynchronous actions sequentially (see `docs/asyncDispatch.md`).
-   **`withForward`**: A helper used with `asyncDispatch` to pass results between sequential actions (see `docs/asyncDispatch.md`).
-   **`CRUD`**: The base class for creating store modules with simplified CRUD operations (see `docs/storeModulesCrud.md`).

## `StoreProvider` Component

This component is essential for setting up the Redux environment.

-   **Props:**
    -   `children`: (ReactNode, required) The application components to render within the provider.
    -   `modules`: (Object, required) An object containing the application's store modules (e.g., `{ auth: authModuleInstance, products: productsModuleInstance }`). Each module instance should expose its initial `state` and optionally a `handleAction` method.
    -   `storeMiddlewares`: (Array<Middleware>, optional, default: `[]`) An array of additional Redux middlewares to apply.
-   **Functionality:**
    1.  Calls `createStoreModules` to initialize the Redux store using the provided `modules` and `storeMiddlewares`.
    2.  Applies `redux-thunk` middleware by default to handle asynchronous actions.
    3.  Applies `redux-logger` middleware automatically if `isDevelopment` is true (obtained from the internal `container`).
    4.  Wraps the `children` with `react-redux`'s `<Provider store={store}>`, making the store accessible throughout the component tree.

## Store Creation (`createStoreModules`)

This internal function orchestrates the store setup:

1.  **Middleware Setup:** Starts with `redux-thunk`. Adds `redux-logger` if in development mode. Appends any custom `storeMiddlewares` passed to `StoreProvider`.
2.  **Reducer Generation:**
    -   Iterates through the `modules` object provided to `StoreProvider`.
    -   For each module, it dynamically creates a reducer function (`createReducer`).
    -   This reducer uses the module's `state` as the initial state.
    -   It looks for a `handleAction` method within the module. If found, it calls this method to get an object mapping action handler names (camelCased action types) to functions that update the state.
    -   When an action is dispatched, the reducer checks if a corresponding handler exists in the module's `handleAction` result. If yes, it calls the handler with the `action` and current `state` slice and merges the result into the new state.
    -   If no handler matches, it returns the current state slice.
3.  **Combine Reducers:** Uses Redux's `combineReducers` to merge the dynamically generated reducers for each module into a single root reducer.
4.  **Create Store:** Uses Redux's `createStore` with the root reducer and the configured middleware chain.
5.  **Singleton:** Ensures only one store instance is created.

## Usage Example (Connecting a Component)

This example shows a React Native component that displays user information after verifying their session on app launch.

```jsx
import React, { useEffect } from 'react';
import { View, Text, ActivityIndicator, StyleSheet } from 'react-native';
import { store } from 'react-native-appexa';
import authModule from '../storeModules/auth'; // Assuming authModule exports actions

function UserProfile() {
  // Select data from the 'auth' slice of the store
  const { isLogin, user, verifyPending } = store.useSelector(state => state.auth);
  const dispatch = store.useDispatch();

  useEffect(() => {
    // This action should be dispatched once when the app starts,
    // for example, in the root component.
    if (verifyPending) { // Only dispatch if we haven't verified yet
        dispatch(authModule.verifyToken()); 
    }
  }, [dispatch, verifyPending]);

  if (verifyPending) {
    return (
      <View style={styles.container}>
        <ActivityIndicator size="large" />
        <Text>Verifying session...</Text>
      </View>
    );
  }

  if (!isLogin) {
    return (
      <View style={styles.container}>
        <Text>Please log in.</Text>
      </View>
    );
  }

  return (
    <View style={styles.container}>
      <Text style={styles.title}>Welcome, {user?.name}!</Text>
      {/* Display other user info */}
    </View>
  );
}

const styles = StyleSheet.create({
    container: {
        flex: 1,
        justifyContent: 'center',
        alignItems: 'center',
        padding: 20
    },
    title: {
        fontSize: 24,
        fontWeight: 'bold'
    }
});

export default UserProfile;
```

This structure allows for modular state management where each feature (like auth, products, etc.) encapsulates its own state, actions, and reducers within a dedicated module class.
