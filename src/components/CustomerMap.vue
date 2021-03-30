<template>
  <view :style="styles.container">
    <MapView
      :style="styles.map"
      :initialRegion="initialRegion"
      :provider="PROVIDER_GOOGLE"
    >
      <Marker
        v-for="marker in markers"
        :key="marker.id"
        :coordinate="marker.location"
      >
        <Callout :onPress="() => handleMarkerClick(marker)">
          <view>
            <text :style="styles.markerTitle">{{ marker.name }}</text>
            <text>Click to show details</text>
          </view>
        </Callout>
      </Marker>
    </MapView>

    <!-- Drawer -->
    <view :style="styles.drawer" v-if="pressedMarker">
      <text :style="styles.lightTitle">{{ currentPharmacyName }}</text>
      <text :style="styles.lightTextSmall"
        >Address: {{ currentPharmacyAddress }}</text
      >
      <text />
      <view :style="styles.textContainer">
        <text :style="styles.lightText">{{ currentPharmacyDescription }}</text>
      </view>
      <text />
      <view>
        <button title="Chat" @press="createChatRoom" />
      </view>
    </view>
  </view>
</template>

<script>
import MapView, { Callout, Marker, PROVIDER_GOOGLE } from 'react-native-maps';
import firebase from 'firebase/app';
import 'firebase/database';
import { StyleSheet } from 'react-native';

const BANGKOK_REGION = {
  latitude: 13.7563,
  longitude: 100.5018,
  latitudeDelta: 0.0922,
  longitudeDelta: 0.0421,
};
// const API_KEY = 'AIzaSyC8oXnYPjm2GihFIjDsFt9iwDfCflvcRos';

const database = firebase.database();
const styles = StyleSheet.create({
  container: {
    ...StyleSheet.absoluteFillObject,
    justifyContent: 'flex-end',
    alignItems: 'center',
  },
  map: {
    ...StyleSheet.absoluteFillObject,
  },
  markerTitle: {
    fontSize: 20,
    fontWeight: 'bold',
  },
  drawer: {
    backgroundColor: '#343039',
    width: '100%',
    height: '40%',
    borderTopLeftRadius: 40,
    borderTopRightRadius: 40,
    padding: 30,
  },
  textContainer: {
    maxHeight: '35%',
    overflow: 'hidden',
  },
  lightText: {
    color: '#f8f8ff',
  },
  lightTextSmall: {
    color: '#f8f8ff',
    fontSize: 11,
  },
  lightTitle: {
    color: '#f8f8ff',
    fontSize: 26,
    fontWeight: 'bold',
  },
});

export default {
  components: {
    MapView,
    Marker,
    Callout,
  },
  props: {
    navigation: {
      type: Object,
    },
  },
  data() {
    return {
      pharmacies: {},
      markers: [],
      pharmaciesRef: null,
      initialRegion: BANGKOK_REGION,
      currentPharmacyId: '',
      currentPharmacyName: '',
      currentPharmacyDescription: '',
      currentPharmacyAddress: '',
      PROVIDER_GOOGLE,
      pressedMarker: false,
      styles,

      username: 'sickperson',
      uid: '4XulcO49PARP3PzjhZBkwOeMZYM2',
    };
  },
  created() {
    this.pharmaciesRef = database.ref('/registered-pharmacies');
  },
  async mounted() {
    await this.setPharmacies();
    // console.log(this.pharmacies, 'before createmarker');
  },
  methods: {
    async setPharmacies() {
      await this.pharmaciesRef.on('value', (pharmaciesSnap) => {
        const pharms = {};
        pharmaciesSnap.forEach((pharmacy) => {
          pharms[pharmacy.key] = pharmacy.val();
        });
        this.pharmacies = pharms;
        this.createMarkers();
      });
    },
    createMarkers() {
      if (this.pharmacies) {
        Object.entries(this.pharmacies).forEach((entry) => {
          const [key, pharmacy] = entry;
          const newMarker = {
            latitude: pharmacy.location.lat,
            longitude: pharmacy.location.lng,
          };
          this.markers.push({
            id: key,
            name: pharmacy.name,
            description: pharmacy.description,
            address: pharmacy.address,
            location: newMarker,
          });
        });
      }
    },
    handleMarkerClick(marker) {
      this.pressedMarker = true;
      this.currentPharmacyId = marker.id;
      this.currentPharmacyName = marker.name;
      this.currentPharmacyDescription = marker.description;
      this.currentPharmacyAddress = marker.address;
    },
    cancelClick() {
      this.pressedMarker = false;
    },
    async getPharmacyUserId(key) {
      console.log(key);
      const id = await database
        .ref(`registered-pharmacies/${key}/owner`)
        .once('value')
        .then((snapshot) => snapshot.val());
      return id;
    },
    async generateChatRoomID() {
      const getRandomInt = function (min, max) {
        return Math.floor(Math.random() * (max - min + 1)) + min;
      };
      const generate = function () {
        const length = 8;
        const timestamp = +new Date();
        const ts = timestamp.toString();
        const parts = ts.split('').reverse();
        let id = '';
        for (let i = 0; i < length; i += 1) {
          const index = getRandomInt(0, parts.length - 1);
          id += parts[index];
        }
        return id;
      };

      const id = generate();
      const hasDup = await this.childExist('messages/chatUID', id);
      if (hasDup) {
        return this.generateChatRoomID();
      }
      return id;
    },
    async childExist(path, child) {
      const snapshot = await firebase.database().ref(path).once('value');
      const hasChild = snapshot.hasChild(child);
      return hasChild;
    },
    async createChatRoom() {
      // REQUIRED ANOTHER USER ID TO CREATE A CHAT ROOM pharmacyId
      const pharmacyId = await this.getPharmacyUserId(this.currentPharmacyId);
      let hasChild = await this.childExist(`user/${this.uid}`, 'chatRooms');
      // console.log(hasChild);
      const roomID = await this.generateChatRoomID();
      // this.roomID = roomID;
      if (!hasChild) {
        firebase
          .database()
          .ref(`user/${this.uid}`)
          .child('chatRooms')
          .push(roomID); // add to
      } else {
        firebase.database().ref(`user/${this.uid}/chatRooms`).push(roomID);
      }
      hasChild = await this.childExist(`user/${pharmacyId}`, 'chatRooms');
      if (!hasChild) {
        firebase
          .database()
          .ref(`user/${pharmacyId}`)
          .child('chatRooms')
          .push(roomID); // add to
      } else {
        firebase.database().ref(`user/${pharmacyId}/chatRooms`).push(roomID);
      }
      firebase
        .database()
        .ref('messages/chatRooms/')
        .child(roomID)
        .child('members')
        .update({ member1: this.username, member2: this.currentPharmacyName });
      firebase
        .database()
        .ref(`messages/chatRooms/${roomID}`)
        .child('messages')
        .push({
          from: this.username,
          text:
            'This message is send by the system. Start your conversation here!',
          timestamp: Date.now(),
        }); // add messages
      // this.$router.push({ name: 'chat', params: { roomID } });
      this.navigation.navigate('Chat', { id: roomID });
    },
  },
};
</script>
