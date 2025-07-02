# Store Module Example: Authentication (`/docs/storeModules.md`)

This document provides a detailed example of creating an authentication store module for a React Native application using the patterns encouraged by `react-native-appexa`. It uses `@react-native-async-storage/async-storage` for persistence.

## 1. Module Definition (`src/storeModules/auth/index.js`)

First, define the `Auth` class and export an instance.

```jsx
import { request } from 'react-native-appexa'; // Import request for API calls
import AsyncStorage from '@react-native-async-storage/async-storage';

class Auth {
  // Define a key for storing login data in AsyncStorage
  asyncStorageLoginKey = 'appexaAuthToken';

  // --- State --- 
  get state() {
    // The initial state is always logged out.
    // An action like `verifyToken` should be dispatched on app start to check for a stored token.
    return {
      pending: false,       // General pending state for the login process
      isLogin: false,       // User is not considered logged in initially
      error: null,          // Stores login/verify error messages
      verifyPending: true,  // Start in a pending state to check the token on app launch
      user: null            // To store user data after login/verification
    };
  }

  // --- Constants --- 
  get constants() {
    return {
      VERIFY_TOKEN_SUCCESS: "VERIFY_TOKEN_SUCCESS",
      VERIFY_TOKEN_ERROR: "VERIFY_TOKEN_ERROR",
      LOGIN_PENDING: "LOGIN_PENDING",
      LOGIN_SUCCESS: "LOGIN_SUCCESS",
      LOGIN_ERROR: "LOGIN_ERROR",
      LOGOUT: "LOGOUT"
    };
  }

  // --- Action Creators --- 
  verifyTokenSuccess = (data) => ({ type: this.constants.VERIFY_TOKEN_SUCCESS, data });
  verifyTokenError = (error) => ({ type: this.constants.VERIFY_TOKEN_ERROR, data: error });
  loginPending = () => ({ type: this.constants.LOGIN_PENDING });
  loginSuccess = (data) => ({ type: this.constants.LOGIN_SUCCESS, data });
  loginError = (errorMessage) => ({ type: this.constants.LOGIN_ERROR, data: errorMessage });
  logout = () => ({ type: this.constants.LOGOUT });

  // --- Thunk Actions (Asynchronous Logic) ---

  // Action to handle user login
  login = (email, password) => async (dispatch) => {
    dispatch(this.loginPending());
    try {
      const response = await request.login({ email, password }); // Assumes 'login' endpoint in appexa.json
      const token = response?.data?.accessToken; // Adjust based on actual API response
      if (!token) {
        throw new Error(response?.message || "Login failed: No token received");
      }
      
      request.setHeader("x-access-token", token);
      await AsyncStorage.setItem(this.asyncStorageLoginKey, JSON.stringify({ token }));
      
      dispatch(this.loginSuccess(response.data)); // Pass user data if available
      return response.data;
    } catch (error) {
      const message = error?.data?.message || error?.message || "Login failed";
      dispatch(this.loginError(message));
      return Promise.reject(message);
    }
  };

  // Action to verify an existing token from AsyncStorage (e.g., on app load)
  verifyToken = () => async (dispatch) => {
    try {
      const tokenDataString = await AsyncStorage.getItem(this.asyncStorageLoginKey);
      if (!tokenDataString) {
        throw new Error("No token found");
      }

      const { token } = JSON.parse(tokenDataString);
      if (!token) throw new Error("Invalid token data");

      request.setHeader("x-access-token", token);
      const response = await request.verifyToken(); // Assumes 'verifyToken' endpoint exists

      dispatch(this.verifyTokenSuccess({ isLogin: true, user: response.data }));
      return response.data;
    } catch (error) {
      await AsyncStorage.removeItem(this.asyncStorageLoginKey);
      request.setHeader("x-access-token", "");
      dispatch(this.verifyTokenError(error.message));
      return Promise.reject(error);
    }
  };

  // Action to handle user logout
  logoutUser = () => async (dispatch) => {
    await AsyncStorage.removeItem(this.asyncStorageLoginKey);
    request.setHeader("x-access-token", "");
    dispatch(this.logout());
  };

  // --- Reducer Logic --- 
  handleAction() {
    return {
      loginPending: (action, state) => ({ ...state, pending: true, error: null }),
      loginSuccess: (action, state) => ({ ...state, pending: false, isLogin: true, error: null, user: action.data?.user }),
      loginError: (action, state) => ({ ...state, pending: false, isLogin: false, error: action.data }),
      
      verifyTokenSuccess: (action, state) => ({
        ...state,
        isLogin: action.data.isLogin,
        user: action.data.user,
        verifyPending: false,
        error: null
      }),
      verifyTokenError: (action, state) => ({
        ...state,
        isLogin: false,
        user: null,
        verifyPending: false,
        error: action.data
      }),

      logout: (action, state) => ({
        ...state,
        isLogin: false,
        user: null,
        pending: false,
        error: null
      })
    };
  }
}

export default new Auth();
```

## 2. Register the Module (`src/storeModules/index.js`)

Ensure the exported instance is included in your main store module aggregation file.

```jsx
import auth from './auth';
// import other modules...

export default {
  auth,
  // other modules...
};
```

This example illustrates how to structure a store module for React Native with state initialization, constants, action creators, and asynchronous thunks interacting with the `request` module and `AsyncStorage`.
