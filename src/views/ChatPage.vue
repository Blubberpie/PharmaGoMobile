<template>
  <view>
    <view class="header">
      <!-- <image :source="require('../../assets/user-male.png')" class="profile-pic"/> -->
      <!-- <ion-icon name="arrow-back-outline"></ion-icon> -->
      <touchable-opacity :on-press="()=>test()" :disabled="(text == '')">
            <view  class="button-view">
              <text class="color-white">back</text>
            </view>
      </touchable-opacity> 
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
      <!-- <view v-for="(item, index) in messages" :key="index">
        <view v-if="item">
          <view
            :class="[
              'd-flex flex-row align-center my-2',
              item.from == username ? 'justify-end' : null,
            ]"
          >
            <span v-if="item.from == username" class="blue--text mr-3">{{
              item.text
            }}</span>
            <v-avatar :color="item.from == username ? 'indigo' : 'red'" size="36">
            </v-avatar>
            <span v-if="item.from != username" class="blue--text ml-3">{{
              item.text
            }}</span>
          </view>
        </view>
      </view> -->
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
    
    
    <!-- <div v-if="roomID">
    <v-container fluid fill-height>
      <v-layout>
        <v-flex xs12 sm9>
          <v-card justify="left" height="700px">
            <v-toolbar dark color="primary darken-1">
              <v-toolbar-title>Chat {{ username }}</v-toolbar-title>
            </v-toolbar>
            <v-card-text>
              <v-list class="logs">
                <div v-for="(item, index) in messages" :key="index">
                  <div v-if="item">
                    <div
                      :class="[
                        'd-flex flex-row align-center my-2',
                        item.from == username ? 'justify-end' : null,
                      ]"
                    >
                      <span v-if="item.from == username" class="blue--text mr-3">{{
                        item.text
                      }}</span>
                      <v-avatar :color="item.from == username ? 'indigo' : 'red'" size="36">
                      </v-avatar>
                      <span v-if="item.from != username" class="blue--text ml-3">{{
                        item.text
                      }}</span>
                    </div>
                  </div>
                </div>
              </v-list>
            </v-card-text>
            <v-spacer></v-spacer>
            <v-card-actions class="card-actions">
              <v-text-field v-model="text" label="Message" single-line></v-text-field>
              
              <v-btn icon class="ml-4" @click="send"><v-icon>mdi-send</v-icon></v-btn>
            </v-card-actions>
          </v-card>
        </v-flex>
      </v-layout>
    </v-container>
  </div> -->
  </view>
</template>

<script>
import { Ionicons } from "@expo/vector-icons";
import firebase from '../plugins/firebase';
import React, {Component} from 'react';
import {
View, Dimensions, Text, KeyboardAvoidingView 
} from 'react-native';

export default {
  name: 'Chat',
  components: {Ionicons},
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
              }}>{ message.item.from[0] }</Text>
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
    test() {
      // alert(this.roomIDs);
      // const username = await firebase.database()
      //     .ref(`user/${this.uid}/credentials/username`)
      //     .once('value')
      //     .then((snapshot) => snapshot.val());
      alert(testing);
      // alert(username);
    },
  },
  // mounted() {
  async mounted() {
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
