<template>
  <view class="container">
    <text class="header">Chats </text>
    <view :style="{ height: 600 }">
      <scroll-view
        :content-container-style="{ contentContainer: { flex: 1, height: 10 } }"
      >
        <flat-list :data="rooms" :render-item="(room) => renderRooms(room)">
        </flat-list>
      </scroll-view>
    </view>
  </view>
</template>

<script>
import { Badge } from 'react-native-paper';
import firebase from '../plugins/firebase';
import React, { Component } from 'react';
import { View, Dimensions, Text, TouchableOpacity } from 'react-native';

export default {
  name: 'ChatList',
  components: { Badge },
  props: {
    navigation: {
      type: Object,
    },
  },
  data() {
    return {
      rooms: [],
      otherMember: '',
      roomIDs: [],
      currentRoomID: '',
      username: '',
      testing: '',
      database: null,

      username: 'pharmacy1',
      uid: 'GVvPLsMTwoNdjt6r4J3F6NDvgwl1',
      testing: '',
    };
  },
  methods: {
    renderRooms(room) {
      return (
        <TouchableOpacity onPress={() => this.chat(room.item.id)}>
          <View
            style={{
              flexDirection: 'row',
              flex: 1,
              borderRadius: 10,
              padding: 10,
              margin: 5,
            }}
          >
            <Badge>{room.item.member[0]}</Badge>
            <Text>{room.item.member}</Text>
          </View>
        </TouchableOpacity>
      );
    },
    setCurrentRoomID(id) {
      this.currentRoomID = id;
    },
    async getAllUsersChatRooms() {
      const rooms = [];
      let val = {};

      await firebase
        .database()
        .ref(`user/${this.uid}/chatRooms`)
        .once('value')
        .then((snapshot) => {
          val = snapshot.val();
          Object.keys(val).forEach((key) => {
            rooms.push(val[key]);
          });
        });
      this.roomIDs = rooms;
      return rooms;
    },
    async getOtherRoomMember(id) {
      let members = {};
      await firebase
        .database()
        .ref(`messages/chatRooms/${id}/members`)
        .once('value')
        .then((snapshot) => {
          const obj = snapshot.val();
          members = Object.values(obj);
        });
      const otherMember = members.filter(
        (member) => member !== this.username
      )[0];
      this.otherMember = otherMember;
      return otherMember;
    },
    async setAllRooms() {
      this.roomIDs = await this.getAllUsersChatRooms();

      const newID = null;
      if (newID) {
        this.setCurrentRoomID(newID);
      } else {
        this.setCurrentRoomID(this.roomIDs[0]);
      }
      this.roomIDs.forEach(async (roomID) => {
        const member = await this.getOtherRoomMember(roomID);
        this.rooms.push({
          member,
          id: roomID,
        });
      });
    },
    async setUsername() {
      const username = await firebase
        .database()
        .ref(`user/${this.uid}/credentials/username`)
        .once('value')
        .then((snapshot) => snapshot.val());
      this.username = username;
    },
    chat(id) {
      this.navigation.navigate('Chat', { id });
    },
  },
  async mounted() {
    await this.setUsername();
    await this.setAllRooms();
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
.header {
  font-size: 35;
  font-weight: bold;
}
</style>
