# Using the Validation Module

The `validation` module in `react-native-appexa` provides a set of functions to validate form data and other inputs. This module helps ensure that user input meets the expected criteria before being processed or sent to an API.

#### Basic Usage

The `validation` module allows you to define validation rules for specific fields and then validate input against those rules.

Here is an example of a simple login form in React Native:

```javascript
import React, { useState } from 'react';
import { View, TextInput, Button, Alert, StyleSheet } from 'react-native';
import { validation } from 'react-native-appexa';

const LoginScreen = () => {
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');

  const handleLogin = () => {
    const validationSchema = {
      email: {
        fieldTitle: 'Email',
        type: 'email',
      },
      password: {
        fieldTitle: 'Password',
        type: 'password', // This checks for a minimum length (default 4)
        typeOptions: { min: 6 } // Override default min length
      }
    };

    const validationResponse = validation(validationSchema, { email, password });

    if (!validationResponse.status) {
      Alert.alert('Validation Error', validationResponse.message);
      return;
    }

    // If validation passes, proceed with login logic
    Alert.alert('Success', 'Login data is valid!');
    // dispatch(authModule.login(email, password));
  };

  return (
    <View style={styles.container}>
      <TextInput
        style={styles.input}
        placeholder="Email"
        value={email}
        onChangeText={setEmail}
        keyboardType="email-address"
        autoCapitalize="none"
      />
      <TextInput
        style={styles.input}
        placeholder="Password"
        value={password}
        onChangeText={setPassword}
        secureTextEntry
      />
      <Button title="Login" onPress={handleLogin} />
    </View>
  );
};

const styles = StyleSheet.create({
  container: { flex: 1, justifyContent: 'center', padding: 20 },
  input: { height: 40, borderColor: 'gray', borderWidth: 1, marginBottom: 12, padding: 10 }
});

export default LoginScreen;
```

### Validation Rules

Below are the available validation types and their configurations.

#### `required`
Checks if a field is not `undefined`.
```javascript
fullName: {
  fieldTitle: "Full Name",
  type: "required"
}
```

#### `string`
Checks if the value is a string.
```javascript
username: {
  fieldTitle: "Username",
  type: "string"
}
```

#### `number`
Checks if the value is a number.
```javascript
age: {
  fieldTitle: "Age",
  type: "number"
}
```

#### `email`
Checks if the value is in a valid email format.
```javascript
email: {
  fieldTitle: "Email",
  type: "email"
}
```

#### `password`
Checks for a minimum string length. The default is 4.
```javascript
password: {
  fieldTitle: "Password",
  type: "password",
  typeOptions: { min: 8 } // Optional: override default
}
```

#### `length`
Checks if the value's length is within a given range.
```javascript
username: {
  fieldTitle: "Username",
  type: "length",
  typeOptions: { min: 3, max: 10 }
}
```

#### `array`
Checks if the value is an array.
```javascript
tags: {
  fieldTitle: "Tags",
  type: "array"
}
```

#### `oneOf`
Checks if the value is one of the specified values in an array.
```javascript
role: {
  fieldTitle: "Role",
  type: "oneOf",
  typeOptions: ["admin", "user", "guest"] // The options are the array itself
}
```

#### `isObject`
Checks if the value is a plain object.
```javascript
userDetails: {
  fieldTitle: "User Details",
  type: "isObject"
}
```

#### `slug` & `nestedSlug`
Checks for specific URL-friendly slug formats.
```javascript
categorySlug: {
  fieldTitle: "Category Slug",
  type: "slug" // or "nestedSlug"
}
```
