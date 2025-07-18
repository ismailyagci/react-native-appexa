# react-native-appexa

`react-native-appexa` is an npm package that provides the basic infrastructure and tools for React Native project. By using this package, you can speed up the setup process of your React Native project and make your development process more efficient.

For a full list of documentation, see the [Documentation Index](./docs/index.md).

## Features

- **Fast Setup**: Quick and easy setup for your React Native project.
- **Customizable Structure**: Easily customizable structure according to your project's needs.
- **Performance Optimizations**: Various optimizations to improve your project's performance.
- **API Configuration**: Easy API management and configuration with `appexa.json`.
- **VSCode IntelliSense Integration**: Enhanced code completion and debugging features with the `appexa` VSCode extension.

## Installation

### npm

To add the `react-native-appexa` package to your project with npm, you can use the following command:

```bash
npm install react-native-appexa
```

### appexa.json

After installation, create a file named `appexa.json` in the root folder of your project. This file is used for managing API requests and has the following features:

- **API Endpoint Configuration**: The type (get, post, etc.), URL, and additional headers if necessary can be specified for each API operation.
- **Centralized Management**: All API requests are managed from a single file, making project maintenance and updates easier.
- **Flexibility**: You can customize the API configuration by making changes to the `appexa.json` file as needed.

For more details on configuring requests, see the [Request Module documentation](./docs/request.md).

Example `appexa.json` configuration:

```json
{
  "request": {
    "baseUrl": "https://api.example.com/",
    "createItem": {
      "type": "post",
      "url": "item/createItem"
    }
  }
}
```

### VSCode Plugin

You can enable IntelliSense features by installing the [appexa](https://marketplace.visualstudio.com/items?itemName=371digital.appexa) extension in your VSCode IDE. This extension works in harmony with your `appexa.json` file, facilitating your coding and debugging processes.

## Usage

### Provider

You need to wrap your application with the `Appexa` provider. To do this, modify your root component file (e.g., `App.js`) as follows:

For more details, see the [Provider Component documentation](./docs/provider.md).

```jsx
// App.js
import React from 'react';
import Appexa from 'react-native-appexa';
import storeModules from './src/storeModules'; // Assuming your modules are in src
import appexaConfig from './appexa.json';
import AppNavigator from './src/navigation'; // Your app's navigator/main component

const App = () => {
  return (
    <Appexa
      storeModules={storeModules}
      config={appexaConfig}
      isDevelopment={__DEV__} // Use React Native's global __DEV__ variable
    >
      <AppNavigator />
    </Appexa>
  );
};

export default App;
```

### Store Modules

You can manage your Redux Store processes with `react-native-appexa`. Create a folder named `storeModules` inside your application's `src` directory and add an `index.jsx` file to this folder. This file will bring together your Redux store modules:

```jsx
export default {};
```

For a detailed guide on creating store modules, see the [Auth Module Example](./docs/storeModules.md). For simplifying CRUD operations, refer to the [Using the CRUD Class documentation](./docs/storeModulesCrud.md).

### Request

The request module is designed to simplify the management of API requests. For details, you can refer to the [Request Module documentation](./docs/request.md).

### Other Features

`react-native-appexa` also includes other utilities:

- **Validation**: A powerful module for data validation. See the [Validation documentation](./docs/validation.md).
- **Async Dispatch**: Utilities for handling sequential asynchronous Redux actions. See the [Async Dispatch documentation](./docs/asyncDispatch.md).

## License

`react-native-appexa` is licensed under the [MIT License](LICENSE).
