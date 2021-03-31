<template>
  <ScrollView class="container">
    <text class="header">PharmaGo </text>
    <view v-if="role === 'Customer'">
      <view class="row" style="background-color: #0099ff">
        <!-- <button title="Map Page " @press="toMapPage" /> -->
        <touchable-opacity :on-press="() => toMapPage()">
          <view>
            <text class="text">Choose Pharmacy</text>
          </view>
        </touchable-opacity>
      </view>
      <view class="row" style="background-color: #0066ff">
        <!-- <button title="to ListChat " @press="toListChat" /> -->
        <touchable-opacity :on-press="() => toListChat()">
          <view>
            <text class="text">Chats </text>
          </view>
        </touchable-opacity>
      </view>
      <view class="row" style="background-color: #0052cc">
        <!-- <button
          title="Pending Prescriptions "
          @press="toPendingPrescriptions"
        /> -->
        <touchable-opacity :on-press="() => toPendingPrescriptions()">
          <view>
            <text class="text">Pending Prescriptions </text>
          </view>
        </touchable-opacity>
      </view>
      <view class="row" style="background-color: #0033aa">
        <!-- <button
          title="Pending Prescriptions "
          @press="toPendingPrescriptions"
        /> -->
        <touchable-opacity :on-press="() => toDeliveryStatus()">
          <view>
            <text class="text">Check Delivery Status</text>
          </view>
        </touchable-opacity>
      </view>
      <view class="row" style="background-color: #000099">
        <!-- <button title="Log out " @press="logout" /> -->
        <touchable-opacity :on-press="() => logout()">
          <view>
            <text class="text">Sign Out </text>
          </view>
        </touchable-opacity>
      </view>
    </view>
    <view v-else>
      <!-- <button title="Driver Map Page" @press="toDriverMapPage" /> -->
      <view class="row" style="background-color: #0099ff">
        <touchable-opacity :on-press="() => toDriverMapPage()">
          <view>
            <text class="text">Job Map </text>
          </view>
        </touchable-opacity>
      </view>
      <view class="row" style="background-color: #0066ff">
        <!-- <button title="Log out " @press="logout" /> -->
        <touchable-opacity :on-press="() => logout()">
          <view>
            <text class="text">Sign Out </text>
          </view>
        </touchable-opacity>
      </view>
    </view>

    <button title="Login" @press="login" />
    <button title="test" @press="test" />
  </ScrollView>
</template>

<script>
// import { Appbar } from 'react-native-paper';
// import React, { Component } from 'react';
import { ScrollView } from 'react-native';

import firebase from 'firebase/app';
import 'firebase/database';

const database = firebase.database();

export default {
  components: { ScrollView },
  props: {
    navigation: {
      type: Object,
    },
  },
  data() {
    return {
      username: 'mock username',
      uid: '',
      role: null,
    };
  },
  mounted() {
    this.uid = firebase.auth().currentUser.uid;
    this.setRole();
    if (this.role === 'Pharmacy') {
      alert('Features not avaliable for user with Pharmacy role');
      this.logout();
    }
  },
  methods: {
    toMapPage() {
      this.navigation.navigate('CustomerMap');
    },
    toPendingPrescriptions() {
      this.navigation.navigate('PendingPrescriptions');
    },
    toListChat() {
      this.navigation.navigate('ListChat');
    },
    toDriverMapPage() {
      this.navigation.navigate('Driver');
    },
    login() {
      this.navigation.navigate('Login');
    },
    logout() {
      firebase
        .auth()
        .signOut()
        .then(() => {
          this.navigation.navigate('Login');
          alert('You have been logged off');
          // Sign-out successful.
        })
        .catch((error) => {
          console.log(error);
          alert('An error has occured');
        });
    },
    async setRole() {
      await database
        .ref(`user/${this.uid}/credentials/role`)
        .once('value')
        .then((snapshot) => {
          console.log(snapshot.val(), 'role');
          this.role = snapshot.val();
        });
    },
    test() {
      console.log(this.role);
    },
    toDeliveryStatus() {
      this.navigation.navigate('Statuses');
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
  padding: 40px;
  text-align: center;
  /* background-color: #1976d2; */
  background-color: white;
  color: #1976d2;
  font-size: 30px;
}
.row {
  flex-direction: row;
  /* align-items: center; */
  justify-content: space-evenly;
  /* justify-content: center; */
  /* border-radius: 40; */
  padding: 50;
}
.text {
  font-size: 25;
  color: white;
}
.button {
  /* border: 50; */
  flex: 1;
}
</style>
