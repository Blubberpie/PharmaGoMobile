<template>
  <view>
    <view class="header">
      <text class="username">
      {{ from }}
      <text>
    </view>
    <view :style='{height: 600}'>
    <scroll-view :content-container-style="{contentContainer: {flex:1, height:10}}">
      <flat-list :data='messages' :render-item="(message) => renderMessage(message)">
      </flat-list>
    </scroll-view>
    </view>
    <view class="message-view">
      <text-input
        v-model="text"
        class="message-box"
        placeholder="Type Message"
      />
      <touchable-opacity :on-press="()=>send()" :disabled="(text == '')">
            <view  class="button-view">
              <text class="color-white">Send</text>
            </view>
      </touchable-opacity>  
    </view>
  </view>
</template>

<script>
import { Button } from 'react-native-paper';
import React, { Component } from 'react';
import {
View, Dimensions, Text 
} from 'react-native';

import firebase from 'firebase/app';
import 'firebase/database';

export default {
  name: 'Chat',
  components: { Button },
  props: {
    navigation: {
      type: Object,
    },
  },
  data() {
    return {
      roomID: '',
      from: '',
      username: "",
      name: null,
      showMessage: '',
      messages: [],
      text: null,
      testing: '',
    };
  },
  methods: {
    renderMessage(message) {
      return(
        <View>
          <View style={{flexDirection:message.item.from == this.username ? 'row-reverse':'row'}}>
            <View style={{flexDirection:message.item.from == this.username ? 'row-reverse':'row'}}>
              <Text style={{
                paddingTop: 2,
                textAlign: 'center',
                margin:5,
                height: 26,
                width: 26,
                borderRadius:13,
                backgroundColor: '#bbb',
              }}>
              { message.item.from[0] }
              </Text>
            </View>
          
            <View style={{
              backgroundColor:'lightblue',
              maxWidth:Dimensions.get('screen').width/1.5,
              borderRadius:10,
              padding:10,
              margin:5,
            }}>
              <Text style={{fontSize:15,}}>{message.item.text}</Text>
            </View>
          </View>
        </View>)
    },
    send() {
      if (this.text !== null && this.text !== '') {
        const message = {
          text: this.text,
          from: this.username,
          timestamp: Date.now(),
        };
        firebase
          .database()
          .ref(`messages/chatRooms/${this.roomID}/messages`)
          .push(message);
        this.text = '';
        this.listAllMessages();
      }
    },
    async valueExist(path, value) {
      const snapshot = await firebase
        .database()
        .ref(path)
        .once('value');
      const userData = snapshot.val();
      return value === userData;
    },
    updateChild(path, updates) {
      firebase
        .database()
        .ref(path)
        .update(updates);
    },
    async listAllMessages() {
      const messages = [];
      const val = await firebase
        .database()
        .ref(`messages/chatRooms/${this.roomID}/messages`)
        .orderByChild('timestamp')
        .once('value')
        .then((snapshot) => snapshot.val());
      Object.keys(val).forEach((key) => {
        messages.push(val[key]);
      });
      this.messages = messages;
    },
    async setUsername() {
      const username = await firebase
        .database()
        .ref(`user/${this.uid}/credentials/username`)
        .once('value')
        .then((snapshot) => snapshot.val());
      this.username = username;
    },
    async setOtherRoomMember() {
      let members = {};
      await firebase
        .database()
        .ref(`messages/chatRooms/${this.roomID}/members`)
        .once('value')
        .then((snapshot) => {
          const obj = snapshot.val();
          members = Object.values(obj);
        });
      const otherMember = members.filter(
        (member) => member !== this.username
      )[0];
      this.from = otherMember;
    },
  },
  async mounted() {
    this.roomID = this.navigation.state.params.id;
    this.uid = firebase.auth().currentUser.uid;
    await this.setUsername();
    await this.setOtherRoomMember();
    firebase
        .database()
        .ref(`messages/chatRooms/${this.roomID}/messages`)
        .on('value', () => {
          this.listAllMessages();
        });
    await this.listAllMessages();
    
    
  },
  watch: {
  },
};
</script>

<style scoped>
.container {
  background-color: white;
  align-items: center;
  justify-content: center;
  flex: 1;
}
.message-box {
  background-color: lightgray;
  padding: 10;
  border-radius: 10;
  width: 300;
}
.username {
  font-size: 20;
}
.text-bold {
  font-size: 20;
  font-weight: bold;
}
.header {
  flex-direction: row;
  height: 70;
  align-items: center;
  justify-content: flex-start;
}
.flex-1 {
  flex: 1;
}
.message-view {
  flex-direction: row;
  justify-content: space-evenly;
  align-items: center;
}
.button-view {
  padding: 10;
  background-color: #0059b3;
  align-items: center;
  text-align: center;
  border-radius: 10;
  margin: 5;
}
.color-white {
  color: white;
}
.logout {
  position: absolute;
  left: 0;
  bottom: 0;
  right: 0;
}
.bf {
  background-color: #5c5c3d;
}
</style>
