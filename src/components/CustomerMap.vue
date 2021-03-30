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
        <button title="Chat" />
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
  },
};
</script>
