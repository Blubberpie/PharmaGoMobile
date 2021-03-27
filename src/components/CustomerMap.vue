<template>
  <view class="container">
    Test [{{ pharmacies }}]
    <MapView class="container"
      :initialRegion="initialRegion"
      :provider="PROVIDER_GOOGLE"
    ></MapView>
  </view>
</template>

<script>
import MapView, { PROVIDER_GOOGLE } from 'react-native-maps';
import firebase from 'firebase/app';
import 'firebase/database';

const BANGKOK_REGION = {
  latitude: 13.7563,
  longitude: 100.5018,
  latitudeDelta: 0.0922,
  longitudeDelta: 0.0421,
};
// const API_KEY = 'AIzaSyC8oXnYPjm2GihFIjDsFt9iwDfCflvcRos';

const database = firebase.database();

export default {
  components: {
    MapView,
  },
  data() {
    return {
      pharmacies: {},
      initialRegion: BANGKOK_REGION,
      PROVIDER_GOOGLE,
    };
  },
  created() {
    this.setPharmacies();
  },
  mounted() {
  },
  methods: {
    setPharmacies() {
      const pharmaciesRef = database.ref('/registered-pharmacies');
      pharmaciesRef.on('value', (pharmaciesSnap) => {
        const pharms = {};
        pharmaciesSnap.forEach((pharmacy) => {
          pharms[pharmacy.key] = pharmacy.val();
        });
        this.pharmacies = pharms;
      });
    },
  },
};
</script>

<style>
.container {
  flex: 1;
}
</style>
