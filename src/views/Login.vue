<template>
  <View :style="styles.container">
    <Title :style="styles.title">Login</Title>
    <Text v-if="errorMessage">{{ errorMessage }}</Text>
    <TextInput
      :style="styles.textInput"
      label="Email"
      mode="outlined"
      :error="errorMessage"
      :value="email"
      :onChangeText="(text) => setEmail(text)"
    />
    <TextInput
      :style="styles.textInput"
      :secureTextEntry="true"
      :error="errorMessage"
      label="Password"
      mode="outlined"
      :value="password"
      :onChangeText="(text) => setPassword(text)"
    />
    <PButton mode="contained" :onPress="login"> Login </PButton>
    <PButton mode="contained" :onPress="register"> Register </PButton>
  </View>
</template>

<script>
import firebase from 'firebase/app';
import 'firebase/database';

import { Title, TextInput, Button } from 'react-native-paper';
import { StyleSheet } from 'react-native';

const database = firebase.database();

const styles = StyleSheet.create({
  container: {
    ...StyleSheet.absoluteFillObject,
    paddingTop: 140,
    paddingLeft: '20%',
    paddingRight: '20%',
    alignItems: 'center',
  },
  title: {
    fontSize: 30,
    fontWeight: 'bold',
    paddingBottom: 30,
  },
  textInput: {
    width: '100%',
    marginBottom: 30,
  },
});

export default {
  props: {
    navigation: {
      type: Object,
    },
  },
  components: {
    Title,
    TextInput,
    PButton: Button,
  },
  data() {
    return {
      email: '',
      password: '',
      errorMessage: '',
      styles,
    };
  },
  methods: {
    async login() {
      if (this.email && this.password) {
        await firebase
          .auth()
          .signInWithEmailAndPassword(this.email, this.password)
          .then(
            (userCredential) => {
              const { user } = userCredential;
              this.userCredRef = database.ref(`/user/${user.uid}/credentials/`);
              this.navigation.navigate('Home');
            },
            (err) => {
              this.errorMessage = err.message;
            },
          );
      } else {
        this.errorMessage = 'Cannot be empty!';
      }
    },
    setEmail(email) {
      this.email = email;
    },
    setPassword(password) {
      this.password = password;
    },
    register() {
      this.navigation.navigate('Register');
    },
  },
};
</script>
