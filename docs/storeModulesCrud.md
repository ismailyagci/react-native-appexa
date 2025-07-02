# Using the CRUD Class for Store Modules

`react-native-appexa` provides a `store.CRUD` base class designed to significantly simplify the creation of Redux store modules for common Create, Read, Update, Delete (CRUD) operations. By extending this class, you get automatically generated action types, action creators, initial state structure, and reducer logic for your specified operations.

## Example: Products Module

Let's create a store module to manage products.

**1. Define the Module Class (`src/storeModules/products.js`)**

Create a class that extends `store.CRUD`. In the constructor, call `super()` with configuration options.

```jsx
import { request, store } from "react-native-appexa";

class Products extends store.CRUD {
  constructor() {
    // Define the base names for your CRUD operations
    super({
      items: [
        "CREATE_PRODUCT", // For creating a single product
        "FETCH_PRODUCTS", // For fetching a list of products
        "UPDATE_PRODUCT", // For updating a single product
        "DELETE_PRODUCT"  // For deleting a single product
      ],
      // Optional: Define additional custom state properties
      state: {
        searchTerm: '',
        filterCategory: null
      },
      // Optional: Define additional custom constants
      constants: {
        SET_SEARCH_TERM: "SET_SEARCH_TERM",
        SET_FILTER_CATEGORY: "SET_FILTER_CATEGORY"
      }
    });
  }

  // --- Thunk Action Methods ---

  createProduct = (productData) => async (dispatch) => {
    dispatch(this.createProductPending()); 
    try {
      const response = await request.createProduct(productData); // Assumes 'createProduct' exists in appexa.json
      dispatch(this.createProductSuccess(response)); 
      return response;
    } catch (error) {
      dispatch(this.createProductError(error?.data || error)); 
      return Promise.reject(error);
    }
  };

  fetchProducts = (params) => async (dispatch) => {
    dispatch(this.fetchProductsPending());
    try {
      const response = await request.getProducts(params); // Assumes 'getProducts' exists in appexa.json
      dispatch(this.fetchProductsSuccess(response));
      return response;
    } catch (error) {
      dispatch(this.fetchProductsError(error?.data || error));
      return Promise.reject(error);
    }
  };

  // ... other actions like updateProduct, deleteProduct
}

export default new Products();
```

**2. Register the Module (`src/storeModules/index.js`)**

```jsx
import auth from './auth';
import products from './products'; // Import the new module

export default {
  auth,
  products, // Add it to the export
};
```

**3. Using the Module in a React Native Component**

Here's how you might use the `products` module in a component to display a list of products.

```jsx
import React, { useEffect } from 'react';
import { View, Text, FlatList, ActivityIndicator, Button, StyleSheet } from 'react-native';
import { store } from 'react-native-appexa';
import productsModule from '../storeModules/products'; // Import your module instance

const ProductListScreen = () => {
  const dispatch = store.useDispatch();
  
  // Select the relevant state from the 'products' slice
  const { data, pending, error } = store.useSelector(state => state.products.fetchProducts);

  useEffect(() => {
    // Fetch products when the component mounts
    dispatch(productsModule.fetchProducts());
  }, [dispatch]);

  const handleAddProduct = () => {
    const newProduct = { name: 'New Gadget', price: 99.99 };
    dispatch(productsModule.createProduct(newProduct))
      .then(() => {
        // After creating, refresh the list
        dispatch(productsModule.fetchProducts());
      });
  };

  if (pending) {
    return <ActivityIndicator style={styles.centered} size="large" />;
  }

  if (error) {
    return <Text style={styles.error}>Error fetching products: {error.message}</Text>;
  }

  return (
    <View style={styles.container}>
      <Button title="Add New Product" onPress={handleAddProduct} />
      <FlatList
        data={data || []} // Ensure data is an array
        keyExtractor={(item) => item.id.toString()}
        renderItem={({ item }) => (
          <View style={styles.itemContainer}>
            <Text style={styles.itemName}>{item.name}</Text>
            <Text>${item.price}</Text>
          </View>
        )}
      />
    </View>
  );
};

const styles = StyleSheet.create({
  centered: { flex: 1, justifyContent: 'center', alignItems: 'center' },
  container: { flex: 1, padding: 10 },
  error: { color: 'red', textAlign: 'center', marginTop: 20 },
  itemContainer: { padding: 15, borderBottomWidth: 1, borderBottomColor: '#ccc' },
  itemName: { fontSize: 18, fontWeight: 'bold' }
});

export default ProductListScreen;
```

## Benefits

-   **Reduced Boilerplate:** Automatically handles the creation of constants, action creators (pending, success, error, reset), initial state slices, and basic reducer logic for each CRUD item.
-   **Consistency:** Enforces a consistent pattern for handling asynchronous CRUD operations.
-   **Extensibility:** Allows adding custom state, constants, actions, and overriding reducer logic easily.