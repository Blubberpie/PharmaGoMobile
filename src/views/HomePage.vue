<template>
  <view class="container">
    <!-- <status-bar background-color="blue" bar-style="light-content" /><view> -->
    <text class="header">Home</text>
    <button title="Press me!" @press="exclaim" />
    <button title="Map Page" @press="toMapPage" />
    <button title="Pending Prescriptions" @press="toPendingPrescriptions" />
    <button title="to ListChat" @press="toListChat" />
    <button title="Drvier Job Page" @press="toDriverPage" />
    <button title="Drvier Map Page" @press="toDriverMapPage" />
    <button title="Login Page" @press="toLoginPage" />
  </view>
</template>

<script>
import { Appbar } from 'react-native-paper';
import React from 'react';
import { View, Dimensions, Text, TouchableOpacity, Header } from 'react-native';

import firebase from 'firebase/app';
import 'firebase/database';

const database = firebase.database();

export default {
  components: { Appbar },
  props: {
    navigation: {
      type: Object,
    },
  },
  data() {
    return {
      message: '',

      username: 'mock username',
      uid: '',
      role: '',
    };
  },
  mounted() {
    this.message = 'Hello World'; // testing mounted
    // let user = firebase.auth().currentUser;
  },
  methods: {
    exclaim() {
      this.message += '!';
      // eslint-disable-next-line no-alert
      alert(this.message);
    },
    toMapPage() {
      this.navigation.navigate('CustomerMap');
    },
    toPendingPrescriptions() {
      this.navigation.navigate('PendingPrescriptions');
    },
    toListChat() {
      this.navigation.navigate('ListChat');
    },
    toDriverPage() {
      this.navigation.navigate('DriverJob');
    },
    toDriverMapPage() {
      this.navigation.navigate('Driver');
    },
    toLoginPage() {
      this.navigation.navigate('Login');
    },
  },
};
</script>

<style>
.container {
  /* background-color: white;
  align-items: center;
  justify-content: center; */
  flex: 1;
}
.text-color-primary {
  color: blue;
}
.header {
  padding: 60px;
  text-align: center;
  background-color: lightblue;
  color: white;
  font-size: 30px;
}
</style>
