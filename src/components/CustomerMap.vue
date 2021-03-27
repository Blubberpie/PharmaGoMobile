<template>
  <view :style="styles.container">
    <MapView :style="styles.map"
      :initialRegion="initialRegion"
      :provider="PROVIDER_GOOGLE"
    >
      <Marker
        v-for="marker in markers" :key="marker.id"
        :coordinate="marker.location"
      >
        <Callout :onPress="()=>handleMarkerClick(marker)">
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
      <text :style="styles.lightTextSmall">Address: {{ currentPharmacyAddress }}</text>
      <text/>
      <view :style="styles.textContainer">
        <text :style="styles.lightText">{{ currentPharmacyDescription }}</text>
      </view>
      <text/>
      <view>
        <button title="Chat"/>
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
      pharmacies: {
        '-MVk2EVqYH8dEPnTD5-U': {
          address: '18 Ratchadamnoen Nok Rd, Bang Khun Phrom, Phra Nakhon, Krung Thep Maha Nakhon 10200, Thailand',
          description: 'Good quality...',
          location: {
            lat: 13.760502698226656,
            lng: 100.50810555219532,
          },
          name: 'Good Pharmacy',
          owner: 'GVvPLsMTwoNdjt6r4J3F6NDvgwl1',
        },
        '-MVk2skXtgZxNuiNuCYf': {
          address: 'Suan Chitralada Palace, Rat Withi Road, Suan Chittralada, Khet Dusit, Bangkok, 10300, แขวง สวนจิตรลดา เขตดุสิต กรุงเทพมหานคร 10300, Thailand',
          description: 'r/ihavereddit r/ihavereddit r/ihavereddit r/ihavereddit r/ihavereddit r/ihavereddit r/ihavereddit r/ihavereddit r/ihavereddit r/ihavereddit r/ihavereddit r/ihavereddit r/ihavereddit r/ihavereddit r/ihavereddit r/ihavereddit r/ihavereddit r/ihavereddit r/ihavereddit r/ihavereddit r/ihavereddit r/ihavereddit r/ihavereddit r/ihavereddit r/ihavereddit r/ihavereddit r/ihavereddit',
          location: {
            lat: 13.770045015645296,
            lng: 100.5216617577553,
          },
          name: 'KarmaPharma',
          owner: 'GVvPLsMTwoNdjt6r4J3F6NDvgwl1',
        },
        '-MVk309vbIfBuFMl_cRT': {
          address: '273 Samsen Rd, Khwaeng Wat Sam Phraya, Khet Phra Nakhon, Krung Thep Maha Nakhon 10200, Thailand',
          description: 'nice nice nice nice nice nice nice nice nice nice nice nice nice nice nice nice nice nice nice nice nice nice nice nice nice nice nice nice nice nice nice nice nice nice nice nice nice nice nice nice nice nice nice nice nice nice nice nice nice nice nice nice nice nice nice nice ',
          location: {
            lat: 13.767740000360437,
            lng: 100.50121796064379,
          },
          name: 'WeedPharma',
          owner: 'GVvPLsMTwoNdjt6r4J3F6NDvgwl1',
        },
        '-MVk4z6wEWaamUVbFAwV': {
          address: '50 Thanon Suppha Mit, Khwaeng Wat Sommanat, Khet Pom Prap Sattru Phai, Krung Thep Maha Nakhon 10100, Thailand',
          description: 'yee yee yee yee yee yee yee yee yee yee yee yee yee yee yee yee yee yee yee yee yee yee yee yee yee yee yee yee yee yee yee yee yee yee yee yee yee yee yee yee yee yee yee yee yee yee yee yee yee yee yee yee yee yee yee yee ',
          location: {
            lat: 13.758188311075239,
            lng: 100.51351052455904,
          },
          name: 'Farmacy',
          owner: 'GVvPLsMTwoNdjt6r4J3F6NDvgwl1',
        },
        '-MVk5DnIEe9rzZUgyY3a': {
          address: '4 ซอย ราชินี Khwaeng Phra Borom Maha Ratchawang, Khet Phra Nakhon, Krung Thep Maha Nakhon 10200, Thailand',
          description: 'actual pharmacy description \nSIKE yeet yeet yeet yeet yeet yeet yeet yeet yeet yeet yeet yeet yeet yeet yeet yeet yeet yeet yeet yeet yeet yeet yeet yeet yeet yeet yeet yeet yeet yeet yeet yeet ',
          location: {
            lat: 13.7585121,
            lng: 100.493434,
          },
          name: 'Actual Pharmacy',
          owner: 'GVvPLsMTwoNdjt6r4J3F6NDvgwl1',
        },
        '-MW3JhfH0Rw2tqIBmGuQ': {
          address: '26 Ratchadamnoen Avenue, Khwaeng Wat Bowon Niwet, Khet Phra Nakhon, Krung Thep Maha Nakhon 10200, Thailand',
          description: 'Some desc',
          location: {
            lat: 13.7572627,
            lng: 100.4974528,
          },
          name: 'Some place ',
          owner: 'GVvPLsMTwoNdjt6r4J3F6NDvgwl1',
        },
      },
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
  mounted() {
    // this.setPharmacies();
    this.createMarkers();
  },
  methods: {
    setPharmacies() {
      this.pharmaciesRef.on('value', (pharmaciesSnap) => {
        const pharms = {};
        pharmaciesSnap.forEach((pharmacy) => {
          pharms[pharmacy.key] = pharmacy.val();
        });
        this.pharmacies = pharms;
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
          this.markers.push(
            {
              id: key,
              name: pharmacy.name,
              description: pharmacy.description,
              address: pharmacy.address,
              location: newMarker,
            },
          );
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
