<template>
  <Provider>
    <app-navigator />
  </Provider>
</template>

<script>
import './src/plugins/firebase';
// declare module 'vue-native-router';
import { Provider } from 'react-native-paper';
import { createAppContainer, createStackNavigator } from 'vue-native-router';

// const VueNativeRouter = require('vue-native-router');

import ChatPage from './src/views/ChatPage.vue';
import ListChatPage from './src/views/ListChatPage.vue';
import HomePage from './src/views/HomePage.vue';
import CustomerMap from './src/components/CustomerMap.vue';
import PendingPrescriptions from './src/components/PendingPrescriptions.vue';
import DriverMap from './src/components/DriverMap.vue';
import Login from './src/views/Login.vue';
import Statuses from './src/views/DeliveryStatuses.vue';
import RegisterPage from './src/views/Register.vue';

const StackNavigator = createStackNavigator(
  {
    Chat: ChatPage,
    ListChat: ListChatPage,
    Home: HomePage,
    CustomerMap,
    PendingPrescriptions,
    Driver: DriverMap,
    Login,
    Statuses,
    Register: RegisterPage,
  },
  {
    initialRouteName: 'Login',
  },
);

const AppNavigator = createAppContainer(StackNavigator);

export default {
  components: { AppNavigator, Provider },
  data() {
    return {
      message: '',
    };
  },
  mounted() {
    this.message = 'Hello World'; // testing mounted
  },
  methods: {
    exclaim() {
      this.message += '!';
      // eslint-disable-next-line no-alert
      alert(this.message);
    },
  },
};
</script>

<style>
.container {
  background-color: white;
  align-items: center;
  justify-content: center;
  flex: 1;
}
.text-color-primary {
  color: blue;
}
</style>
