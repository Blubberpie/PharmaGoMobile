<template>
  <view  class="container">
    <map-view class="map"
        :mapPadding="{ top: 0, right: 0, bottom: dynamic_padding, left: 0 }" 
         :region="currentLocation"
         :provider="PROVIDER_GOOGLE"
         showsMyLocationButton
          showsUserLocation
          :custom-map-style="customMap"
      >
      <text :style="{fontSize:1}">{{new_markers}}</text>
      <map-marker
        v-for="(marker,index) in new_markers"
        :key="index"
            :coordinate="marker.destination"
         >
            <map-view-callout :on-press="()=>handleGetDirection(marker,index)">
              <view>
              <text :style="{color:'grey'}">Press for info</text>
              </view>
			</map-view-callout>
      </map-marker>
        <map-marker :coordinate="pharmacy" :title="pharmacy_name" pinColor="#2b91a7">
        </map-marker>
        <map-view-directions v-if='isUseDirection'
          :origin="currentPosition"
          :destination="destination"
          :apikey="GOOGLE_MAPS_APIKEY"
          :waypoints="waypoints"
          :stroke-width=4
          stroke-color='green'
          :on-ready="(result) => setDistance(result)"
        />
      </map-view>
      <!-- ACCEPT JOB -->
        <view class="accepting_stage" v-if="isAccepted">
            <view :style="{backgroundColor:'#DEDEDE',padding:10,borderTopLeftRadius:10,borderTopRightRadius:10}">
                <view :style="{flexDirection:'row',justifyContent: 'space-between'}">
                  <text :style="{marginLeft:25,fontWeight:'600',fontSize:20}">{{pharmacy_name}}</text>
                 <text :style="{color:'black',fontWeight:'bold',fontSize:15,textAlign:'right',marginRight:10}">{{(30 + ((distance-1)*9)).toFixed(2)}} ฿</text>
                </view>
                 <text :style="{color:'grey',fontWeight:'bold',textAlign:'right',fontSize:15,marginRight:10}">{{distance}} km</text>
            </view>
            <view
                :style="{
                    borderBottomColor: 'grey',
                    borderBottomWidth: 1,
                }"
                />
            <view :style="{ padding:15}">
                <text :style="{fontWeight:'bold',color:'#D4D4D4'}">PICK UP</text>
                <text :style="{color:'black',fontWeight:'400'}">{{pharmacyAddress}}</text>
                <view
                :style="{
                    borderBottomColor: 'grey',
                    borderBottomWidth: 1,
                    marginTop:10,
                    marginBottom:10
                }"
                />
                <text :style="{fontWeight:'bold',color:'#D4D4D4'}">DROP OFF</text>
                <text :style="{color:'black',fontWeight:'400'}">{{customerAddress}}</text>
                <view
                :style="{
                    borderBottomColor: 'grey',
                    borderBottomWidth: 1,
                    marginTop:10
                }"
                />
            </view>
            <view :style="{flexDirection:'row',justifyContent:'flex-end'}">
            <touchable-opacity :on-press="() => ignore(1)" :style="{paddingTop:10}">
            <text :style="{color:'grey',fontWeight:'bold'}">Ignore</text>
          </touchable-opacity>
          <touchable-opacity :on-press="() => accept(index, 2)" :style="{marginLeft:30,marginRight:15,backgroundColor:'green',padding:10,borderRadius:20,width:100}">
            <text :style="{color:'white',fontWeight:'bold',fontSize:16,textAlign:'center'}">Accept</text>
          </touchable-opacity>
            </view>
        </view>
        <!-- PICK-UP STAGE -->
        <view class='card' v-if='isPickUp && isUseCard'>
          <view :style="{marginBottom:20}">
          <touchable-opacity :on-press="close" :style="{backgroundColor:'rgba(105,105,105, 0.3)',position:'absolute',right:0,padding:5,borderRadius:20,width:50}">
            <text :style="{color:'white',textAlign:'center'}">close</text>
          </touchable-opacity>
          </view>
          <view :style="{marginTop:15}">
          <text :style="{color:'grey',marginLeft:50,fontWeight:'bold',fontSize:15,textAlign:'right'}">DISTANCE</text>
          <view :style="{flexDirection: 'row',justifyContent: 'space-between',}">
          <text :style="{fontWeight:'bold',fontSize:18,marginRight:1}">{{pharmacy_name}}</text>
          <text :style="{color:'black',fontWeight:'bold',marginLeft:50,fontSize:15}">{{distance}} km</text>
          </view>
           <view
          :style="{
            borderBottomColor: 'grey',
            borderBottomWidth: 1,
          }"
        />
          <text :style="{color:'grey',fontWeight:'bold',fontSize:15}">Order ready to be picked in</text>
          <view :style="{flexDirection: 'row'}">
            <text :style="{color:'red',fontWeight:'bold',fontSize:20}">{{duration}} min</text>
          <touchable-opacity :on-press="() => pick_up(index, 3)" :style="{backgroundColor:'#008000',position:'absolute',right:0,padding:10,borderRadius:20}">
            <text :style="{color:'white',fontWeight:'bold'}">Start pick-up</text>
          </touchable-opacity>
          </view>
          </view>
        </view>
        <!-- OPEN TOOLBAR -->
        <touchable-opacity v-if="isClose" :on-press="open" :style="{backgroundColor:'rgba(105,105,105, 0.3)',padding:10,borderRadius:20,marginTop:20,alignItems:'center'}">
            <text :style="{color:'white',fontWeight:'bold'}">open</text>
        </touchable-opacity>
        <!-- Sign out -->
        <touchable-opacity v-if="!isClose" :on-press="logout" :style="{backgroundColor:'rgba(255,0,0, 0.3)',width:100,height:40,padding:10,borderRadius:20,marginTop:20,alignItems:'center'}">
          <view>
            <text :style="{color:'red'}">Sign Out </text>
          </view>
        </touchable-opacity>
        <!-- DROP-OFF STAGE -->
        <view class='card' v-if="isDropOff && isUseCard">
          <view :style="{marginBottom:20}">
          <touchable-opacity :on-press="close" :style="{backgroundColor:'rgba(105,105,105, 0.3)',position:'absolute',right:0,padding:5,borderRadius:20,width:50}">
            <text :style="{color:'white',textAlign:'center'}">close</text>
          </touchable-opacity>
          </view>
          <text :style="{color:'black',fontWeight:'bold',marginRight:88,fontSize:20}">Arrive in</text>
          <text :style="{color:'green',fontWeight:'bold',marginRight:88,fontSize:20}">{{distance}} km</text>
          <view
          :style="{
            borderBottomColor: 'grey',
            borderBottomWidth: 1,
          }"
        />
            <text :style="{color:'grey',fontWeight:'bold',fontSize:15}">Press drop off when process finished</text>
          <touchable-opacity :on-press="() => dropoff(index, 4)" :style="{backgroundColor:'#008000',alignItems:'center',padding:10,borderRadius:20}">
            <text :style="{color:'white',fontWeight:'bold'}">DROP OFF</text>
          </touchable-opacity>
        </view>
  </view>
</template>

<script>
import {Switch} from 'react-native';
import  MapView, {Circle, PROVIDER_GOOGLE} from 'react-native-maps';
import * as Location from 'expo-location';
import * as Permissions from 'expo-permissions';
import firebase from 'firebase/app';
import 'firebase/auth';
import 'firebase/database';
import MapViewDirection from 'react-native-maps-directions';
const mapDarkStyle = [
  {
    "elementType": "geometry",
    "stylers": [
      {
        "color": "#212121"
      }
    ]
  },
  {
    "elementType": "labels.icon",
    "stylers": [
      {
        "visibility": "off"
      }
    ]
  },
  {
    "elementType": "labels.text.fill",
    "stylers": [
      {
        "color": "#757575"
      }
    ]
  },
  {
    "elementType": "labels.text.stroke",
    "stylers": [
      {
        "color": "#212121"
      }
    ]
  },
  {
    "featureType": "administrative",
    "elementType": "geometry",
    "stylers": [
      {
        "color": "#757575"
      }
    ]
  },
  {
    "featureType": "administrative.country",
    "elementType": "labels.text.fill",
    "stylers": [
      {
        "color": "#9e9e9e"
      }
    ]
  },
  {
    "featureType": "administrative.land_parcel",
    "stylers": [
      {
        "visibility": "off"
      }
    ]
  },
  {
    "featureType": "administrative.locality",
    "elementType": "labels.text.fill",
    "stylers": [
      {
        "color": "#bdbdbd"
      }
    ]
  },
  {
    "featureType": "poi",
    "elementType": "labels.text.fill",
    "stylers": [
      {
        "color": "#757575"
      }
    ]
  },
  {
    "featureType": "poi.park",
    "elementType": "geometry",
    "stylers": [
      {
        "color": "#181818"
      }
    ]
  },
  {
    "featureType": "poi.park",
    "elementType": "labels.text.fill",
    "stylers": [
      {
        "color": "#616161"
      }
    ]
  },
  {
    "featureType": "poi.park",
    "elementType": "labels.text.stroke",
    "stylers": [
      {
        "color": "#1b1b1b"
      }
    ]
  },
  {
    "featureType": "road",
    "elementType": "geometry.fill",
    "stylers": [
      {
        "color": "#2c2c2c"
      }
    ]
  },
  {
    "featureType": "road",
    "elementType": "labels.text.fill",
    "stylers": [
      {
        "color": "#8a8a8a"
      }
    ]
  },
  {
    "featureType": "road.arterial",
    "elementType": "geometry",
    "stylers": [
      {
        "color": "#373737"
      }
    ]
  },
  {
    "featureType": "road.highway",
    "elementType": "geometry",
    "stylers": [
      {
        "color": "#3c3c3c"
      }
    ]
  },
  {
    "featureType": "road.highway.controlled_access",
    "elementType": "geometry",
    "stylers": [
      {
        "color": "#4e4e4e"
      }
    ]
  },
  {
    "featureType": "road.local",
    "elementType": "labels.text.fill",
    "stylers": [
      {
        "color": "#616161"
      }
    ]
  },
  {
    "featureType": "transit",
    "elementType": "labels.text.fill",
    "stylers": [
      {
        "color": "#757575"
      }
    ]
  },
  {
    "featureType": "water",
    "elementType": "geometry",
    "stylers": [
      {
        "color": "#000000"
      }
    ]
  },
  {
    "featureType": "water",
    "elementType": "labels.text.fill",
    "stylers": [
      {
        "color": "#3d3d3d"
      }
    ]
  }
]
const database = firebase.database();
export default {
    created() {
    this.deliveryJobRef = database.ref('/deliveryJobs');
    this.pharmacyRef = database.ref('/registered-pharmacies');
    this.getLocation();
    },
    async mounted() {
        await this.setDeliveryJob();
  },
  props: {
    navigation: {
      type: Object,
    },
  },
    data() {
    return {
      isAccepted:false,
      isEnabled: false,
      pharmacyAddress: '',
      customerAddress: '',
      isUseDirection: false,
      isPressForInfo:false,
      isPickUp: false,
      isDropOff: false,
      isUseCard: false,
      isClose: false,
      distance:0,
      index:'',
      dynamic_padding:0,
      duration:0,
      pharmacy_name:'',
      GOOGLE_MAPS_APIKEY: 'AIzaSyC8oXnYPjm2GihFIjDsFt9iwDfCflvcRos',
      PROVIDER_GOOGLE: PROVIDER_GOOGLE,
      customMap: mapDarkStyle,
      isClicked: false,
      deliveryJob: {},
      deliveryJobRef: null,
      pharmacies: {},
      pharmacyRef: null,
      markers: [{latitude: 13.70,
      longitude: 100.508},{latitude: 13.760502698226656,
      longitude: 100.50810555219532},{latitude: 13.75,
      longitude: 100.50}],
          marker1: {latitude: 13.760502698226656,
      longitude: 100.50810555219532},marker2: {latitude: 13.75,
      longitude: 100.50},
      currentLocation: null,
      currentPosition: null,
      destination: null,
      waypoints: null,
      location: {},
      pharmacy: null,
      latitude: 0,
      longitude: 0,
      errorMessage: '',
      new_markers: [],
    };
  },
   components: {
    MapView,
    MapMarker: MapView.Marker,
    Circle,
    MapViewDirection,
    MapViewCallout: MapView.Callout,
    PROVIDER_GOOGLE,
    Switch,
  },
  methods: {
    async setDeliveryJob() {
        this.pharmacyRef.on('value', (snapshot) => {
      if (snapshot !== undefined) {
        this.pharmacies = snapshot.val();
      }
    });
       await this.deliveryJobRef.on('value', (snapshot) => {
        this.deliveryJob = snapshot.val();
        this.makeMarkers();
    });
    },
    logout() {
      firebase
        .auth()
        .signOut()
        .then(() => {
          this.navigation.navigate('Login');
          alert('You have been logged off');
        })
        .catch((error) => {
          console.log(error);
          alert('An error has occured');
        });
    },
    accept(index, stage) {
      this.isAccepted = false;
      this.isPickUp = true;
      this.dynamic_padding = 130;
      this.new_markers = [this.new_markers[this.index]];
      this.updateStatus(index, stage);
      // console.log(this.new_markers);;
    },
    pick_up(index, stage) {
      this.isPickUp = false;
      this.isDropOff = true;
      this.updateStatus(index, stage);
      // console.log(this.new_markers);
    },
    dropoff(index, stage){
      alert('Thanks for your dedicated riding!');
      this.ignore(2);
      this.updateStatus(index, stage);
      this.makeMarkers();
      // console.log(this.new_markers);;
    },
    setDistance(result) {
      this.distance = result.distance.toFixed(1);
      this.duration = result.duration.toFixed(1);
    },
    updateStatus(index,stage) {
      this.deliveryJobRef.transaction((snapshot) => {
        Object.entries(snapshot).forEach((key) => {
          if (key[0] === Object.keys(this.deliveryJob)[index]) {
            this.deliveryJobRef.child(key[0]).update({
              status: stage,
            });
          }
        });
      });
    },
      makeMarkers() {
        if (this.deliveryJob) {
            Object.entries(this.deliveryJob).forEach((key) => {
              const [id, job] = key;
              console.log(job.status);
              if (job.status != 4) {
                console.log(job.status);
              const pharmacy_ = this.getName(job.pharmacyId);
              this.new_markers.push({
                  destination:{
                      latitude:job.toLocation.lat,
                      longitude:job.toLocation.lng
                  },
                  waypoints: [{
                      latitude:job.fromLocation.lat,
                      longitude:job.fromLocation.lng
                  }],
                  name: pharmacy_,
                  pharmacyAddress:job.fromAddress,
                  customerAddress:job.toAddress
              });
              }
            });
        }
      },
      getName(p) {
        let data = "";
        Object.entries(this.pharmacies).forEach((key) => {
          if (key[0] === p) {
            data = key[1].name;
          }
        });
      return data;
    },
    close() {this.isUseCard = false; this.isClose = true; this.dynamic_padding = 0;},
    open() {this.isUseCard = true; this.isClose = false; this.dynamic_padding = 130;},
    ignore(n) {
      if (n === 1){
        this.isAccepted = false;
        this.isDropOff = false;
        this.isUseCard = false;
        this.dynamic_padding = 0;
        this.isUseDirection = false;
        this.destination = null;
        this.waypoints = [];
        this.pharmacies ={};
        this.pharmacy = null;
        this.pharmacy_name = ""
        this.pharmacyAddress = "";
        this.customerAddress = "";
      }
      else {
        this.isAccepted = false;
        this.isDropOff = false;
        this.isUseCard = false;
        this.dynamic_padding = 0;
        this.isUseDirection = false;
        this.destination = null;
        this.waypoints = [];
        this.pharmacy = null;
        this.pharmacy_name = ""
        this.pharmacyAddress = "";
        this.customerAddress = "";
      }
    },
    handleGetDirection(marker,index) {
      this.isUseCard = true;
      this.dynamic_padding = 260;
      this.isUseDirection = true;
      this.isAccepted = true;
      this.destination = marker.destination;
      this.waypoints = marker.waypoints;
      this.pharmacy = this.waypoints[0];
      this.pharmacy_name = marker.name
      this.index = index;
      this.pharmacyAddress = marker.pharmacyAddress.replace(',','').split(" ").slice(0,4).join(" ");
      this.customerAddress = marker.customerAddress.replace(',','').split(" ").slice(0,4).join(" ");
      // console.log(this.new_markers);
    },
    
      onRegionChange(region) {
        this.coordinates = region;
},
      getLocation() {
      Permissions.askAsync(Permissions.LOCATION)
        .then(status => {
          if (!status.granted) {
            this.errorMessage = "Permission to access location was denied";
          } else if (status.granted) {
            Location.watchPositionAsync({}, location =>{
              this.location = location;
              this.latitude = location.coords.latitude
              this.longitude = location.coords.longitude
              this.errorMessage = "";
              this.currentLocation = {
                latitude: this.latitude,
                longitude: this.longitude,
                latitudeDelta: 0.0922,
                longitudeDelta: 0.0421
              ,
              };
              this.currentPosition = {
                latitude: this.latitude,
                longitude: this.longitude,
              }
              this.destination = this.currentPosition;
              this.waypoints = this.currentPosition;
          }
        )}
    }) 
  },
},}
</script>
<style>
.accepting_stage{
  background-color: rgb(255, 255, 255);
  position: absolute;
  border-radius: 10;
  height: 250;
  width: 320;
  margin:25;
  bottom: 20;
}
.container {
  position: absolute;
  top: 0;
  left: 0;
  bottom: 0;
  right: 0;
}
.map {
  position: absolute;
  top: 0;
  left: 0;
  bottom: 0;
  right: 0;
}
.description{
  background-color: rgba(0, 0, 0,0.4);
  position: absolute;
  height: 80;
  width: 100%;
  padding: 24;
  bottom: 0;
  margin-bottom: 150;
}
.card{
  background-color: rgb(255, 255, 255);
  position: absolute;
  border-radius: 10;
  height: 160;
  width: 100%;
  padding: 20;
  bottom: 0;
}
</style>

