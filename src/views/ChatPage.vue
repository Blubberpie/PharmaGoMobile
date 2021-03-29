<template>
  <view>
    <view class="header">
      <!-- <image :source="require('../../assets/user-male.png')" class="profile-pic"/> -->
      <!-- <ion-icon name="arrow-back-outline"></ion-icon> -->
      <touchable-opacity :on-press="()=>back()">
            <view  class="button-view">
              <text class="color-white">back</text>
            </view>
      </touchable-opacity> 
      <!-- <Button icon='arrow-left' :on-press="()=>test()"> back</Button> -->
      <text class="username">
      {{ username }}
      <text>
      <!-- <text class="text-color-primary">Chat Page</text> -->
      <!-- <button title="Press me!" @press="test" />
      <text class="text-color-primary">{{ testing }}</text> -->
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
      <!-- <Button icon='camera' :on-press="()=>send()"></Button> -->
      
    </view>
  </view>
</template>

<script>
import { Button } from 'react-native-paper';
import firebase from '../plugins/firebase';
import React, { Component } from 'react';
import {
View, Dimensions, Text, KeyboardAvoidingView 
} from 'react-native';

export default {
  name: 'Chat',
  components: { Button },
  //   props: ['roomID', 'username'],
  data() {
    return {
      roomID: '66516510',
      username: "pharmacy1",
      name: null,
      showMessage: '',
      messages: [],
      text: null,
      testing: '',
    };
  },
  methods: {
    renderMessage(message) {
      // return (<Text> { message.item.text } </Text>)
      return(
        // <View  key={message.item.time}>
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
              // flex: 1,
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
      // this.messages = 
      //   [
      //     {text: 'a'},
      //     {text: 'ab'},
      //     {text: 'aaaaa'},
      //     {text: 'aaaaaaaaa'},
      //     {text: 'aaaaaaaaaaaaa'},
      //     {text: 'aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa'},
      //     {text: 'a'},
      //     {text: 'a'},
      //     {text: 'a'},
      //     {text: 'a'},
      //     {text: 'a'},
      //     {text: 'a'},
      //     {text: 'a'},
      //     {text: 'a'},
      //     {text: 'a'},
      //   ];
    },
    back() {
      // alert(this.roomIDs);
      // const username = await firebase.database()
      //     .ref(`user/${this.uid}/credentials/username`)
      //     .once('value')
      //     .then((snapshot) => snapshot.val());
      // alert('testing');
      // this.$router.push({name: 'ListChat'});
      // alert(username);
    },
  },
  // mounted() {
  async mounted() {
    firebase
        .database()
        .ref(`messages/chatRooms/${this.roomID}/messages`)
        .on('value', () => {
          // console.log(`theres an update in room: ${roomID}`, dataSnapshot.val());
          this.listAllMessages();
        });
    await this.listAllMessages();
    
  },
  watch: {
    // roomID() {
    //   this.listAllMessages();
    // },
  },
};
</script>

<style scoped>
.card-actions {
  flex: 1;
  position: absolute;
  bottom: 10px;
  width: 100%;
}
.logs {
  height: 500px;
  /* overflow: auto; */
}
.container {
  background-color: white;
  align-items: center;
  justify-content: center;
  flex: 1;
}
.text-color-primary {
  color: blue;
}
.message-box {
  background-color: lightgray;
  padding: 10;
  border-radius: 10;
  width: 300;
  /* flex: 1; */
}
.username {
  /* text-align: center; */
  font-size: 20;
}
.text-bold {
  font-size: 20;
  font-weight: bold;
}
.text-regular {
  font-weight: bold;
  color: white;
}
.header {
  flex-direction: row;
  height: 70;
  align-items: center;
  justify-content: flex-start;
  /* background-color: lightblue; */
}
.chat {
  flex: 1;
  position: relative;
}
.scroll {
  flex: 0.6;
}
.profile-pic {
  width: 60;
  height: 60;
  padding: 20;
}
.flex-1 {
  flex: 1;
}
.message-view {
  flex-direction: row;
  justify-content: space-evenly;
  align-items: center;
  /* flex: 1; */
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
