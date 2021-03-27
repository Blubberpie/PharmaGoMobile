<template>
  <!-- <view class="container">
    <text class="text-color-primary">My Vue Native App</text>=
    <button title="Press me!" @press="exclaim" />
  </view> -->
  <app-navigator></app-navigator>
</template>

<script>
// declare module 'vue-native-router';
import {
  createAppContainer,
  createStackNavigator,
} from "vue-native-router";

// const VueNativeRouter = require('vue-native-router');

import ChatPage from "./src/views/ChatPage.vue";
import ListChatPage from "./src/views/ListChatPage.vue";
import HomePage from "./src/views/HomePage.vue";

const StackNavigator = createStackNavigator(
  {
    Chat: ChatPage,
    ListChat: ListChatPage,
    Home: HomePage,
  },
  {
    initialRouteName: 'Home',
  }
);

const AppNavigator = createAppContainer(StackNavigator);

export default {
  components: { AppNavigator },
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
