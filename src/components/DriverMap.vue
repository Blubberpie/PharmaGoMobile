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
      <!-- <text :style="{fontSize:30,color:'red'}">{{ dynamic_padding}}</text> -->
      <!-- <switch v-model="isEnabled" :trackColor="{ false: '#767577', true: '#9adfc2' }" :style="{marginTop: 500}"/> -->
        <!-- <text  v-for="(marker, index) in new_markers" :key="index"> -->
      <map-marker
        v-for="(marker,index) in new_markers"
        :key="index"
            :coordinate="marker.destination" title='asd'
         >
            <map-view-callout :on-press="()=>handleGetDirection(marker)">
              <view>
              <text :style="{color:'grey'}">Press for info</text>
              </view>
			</map-view-callout>
      </map-marker>
        <!-- </text> -->
        <!-- <map-marker :coordinate="pharmacy" title="hi" pinColor="#2b91a7"> -->
          <!-- <image :style="{width: 40,height: 40, borderRadius:25}"
            :source="{uri: 'https://www.pngrepo.com/png/152208/512/pharmacy.png'}"/> -->
            <!-- <map-view-callout >
              <view>
              <text :style="{color:'grey'}">Press for info</text>
              </view>
			</map-view-callout> -->
        <!-- </map-marker> -->
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
        <view class="accepting_stage" v-if="is_accept_stage">
            <view :style="{backgroundColor:'grey',padding:10,borderTopLeftRadius:10,borderTopRightRadius:10}">
                <!-- <text>eqweq</text> -->
                <view :style="{flexDirection:'row',justifyContent: 'space-between'}">
                  <text :style="{marginLeft:25,fontWeight:'600',fontSize:20}">{{pharmacy_name}}</text>
                 <text :style="{fontWeight:'bold',fontSize:15,textAlign:'right',marginRight:10}">{{(distance*12).toFixed(2)}} ฿</text>
                </view>
                 <text :style="{color:'green',fontWeight:'bold',textAlign:'right',fontSize:15,marginRight:10}">{{distance}} km</text>
                 <!-- <text :style="{color:'green',fontWeight:'bold',textAlign:'right',fontSize:15}">{{duration}} min</text> -->
            </view>
            <view
                :style="{
                    borderBottomColor: 'grey',
                    borderBottomWidth: 1,
                }"
                />
            <view :style="{ padding:15}">
                <text :style="{fontWeight:'bold',color:'black'}">PICK UP</text>
                <text :style="{color:'black'}">{{pharmacyAddress}}</text>
                <view
                :style="{
                    borderBottomColor: 'grey',
                    borderBottomWidth: 1,
                    marginTop:10,
                    marginBottom:10
                }"
                />
                <text :style="{fontWeight:'bold',color:'black'}">DROP OFF</text>
                <text :style="{color:'black'}">{{customerAddress}}</text>
                <view
                :style="{
                    borderBottomColor: 'grey',
                    borderBottomWidth: 1,
                    marginTop:10
                }"
                />
            </view>
            <view :style="{flexDirection:'row',justifyContent:'flex-end'}">
            <touchable-opacity :style="{paddingTop:10}">
            <text :style="{color:'grey',fontWeight:'bold'}">Ignore</text>
          </touchable-opacity>
          <touchable-opacity :on-press="accept" :style="{marginLeft:30,marginRight:15,backgroundColor:'green',padding:10,borderRadius:20,width:100}">
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
          <touchable-opacity :on-press="pick_up" :style="{backgroundColor:'#008000',position:'absolute',right:0,padding:10,borderRadius:20}">
            <text :style="{color:'white',fontWeight:'bold'}">Start pick-up</text>
          </touchable-opacity>
          </view>
          </view>
        </view>
        <!-- OPEN TOOLBAR -->
        <!-- <view> -->
        <touchable-opacity v-if="isClose" :on-press="open" :style="{backgroundColor:'rgba(105,105,105, 0.3)',padding:10,borderRadius:20,marginTop:20,alignItems:'center'}">
            <text :style="{color:'white',fontWeight:'bold'}">open</text>
          </touchable-opacity>
        <!-- </view> -->
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
          <touchable-opacity  :style="{backgroundColor:'#008000',alignItems:'center',padding:10,borderRadius:20}">
            <text :style="{color:'white',fontWeight:'bold'}">DROP OFF</text>
          </touchable-opacity>
        </view>
      <!-- <view class='pharmacy'>

      </view> -->
      <!-- <view class='description' v-if='isPressForInfo'>
        <view :style="{flexDirection: 'row',justifyContent: 'space-between',}">
          <text :style="{color:'grey',marginLeft:50,fontWeight:'bold',fontSize:15}">DISTANCE</text>
         <text :style="{color:'grey',marginRight:50,fontWeight:'bold',fontSize:15}">TOTAL TIME</text>
          </view>
       <view :style="{flexDirection: 'row',justifyContent: 'space-between'}">
          <text :style="{color:'green',fontWeight:'bold',marginLeft:50,fontSize:15}">{{distance}} km</text>
         <text :style="{color:'green',fontWeight:'bold',marginRight:63,fontSize:15}">{{duration}} min</text>
        </view>
      </view>
      <view class='toolbar' v-if='isPressForInfo'>
        <view :style="{marginTop:30}">
        <text :style="{fontWeight:'bold',color:'white'}">PICK UP</text>
        <text :style="{fontWeight:'',color:'white'}">{{pharmacyAddress}}</text>
        <view
          :style="{
            borderBottomColor: 'grey',
            borderBottomWidth: 1,
            marginTop:10,
            marginBottom:10
          }"
        />
        <text :style="{fontWeight:'bold',color:'white'}">DROP OFF</text>
        <text :style="{fontWeight:'',color:'white'}">{{customerAddress}}</text>
        </view>
      </view>
      <view class='card' v-if="isUseCard">
        <view v-if="isPickUp">
          <view :style="{marginBottom:40}">
          <touchable-opacity :on-press="close" :style="{backgroundColor:'grey',position:'absolute',right:0,padding:10,borderRadius:20}">
            <text :style="{color:'white',fontWeight:'bold'}">close</text>
          </touchable-opacity>
          </view>
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
          <touchable-opacity :on-press="pick_up" :style="{backgroundColor:'#008000',position:'absolute',right:0,padding:10,borderRadius:20}">
            <text :style="{color:'white',fontWeight:'bold'}">Start pick-up</text>
          </touchable-opacity>
          </view>
        </view>
        <view v-if="isDropOff">
          <view :style="{marginBottom:30}">
          <touchable-opacity :on-press="close" :style="{backgroundColor:'grey',position:'absolute',right:0,padding:10,borderRadius:20}">
            <text :style="{color:'white',fontWeight:'bold'}">close</text>
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
          <touchable-opacity  :style="{backgroundColor:'#008000',alignItems:'center',padding:10,borderRadius:20}">
            <text :style="{color:'white',fontWeight:'bold'}">DROP OFF</text>
          </touchable-opacity>
        </view>
        <view class="job" v-if='isPressForInfo'>
        <view :style="{flexDirection: 'row',justifyContent: 'space-between'}">
          <text :style="{fontWeight:'bold',fontSize:18,marginRight:1}">{{pharmacy_name}}</text>
        <text :style="{fontWeight:'bold',color:'grey',textAlign:'right'}">ESTIMATED PRICE</text>
        </view>
         <text :style="{fontWeight:'bold',fontSize:18,textAlign:'right'}">{{(distance*12).toFixed(2)}} ฿</text>
         
        <view
          :style="{
            borderBottomColor: 'grey',
            borderBottomWidth: 1,
            marginTop:25
          }"
        />
        <touchable-opacity :on-press="accept" v-if="isPressForInfo" :style="{backgroundColor:'#008000',alignItems:'center',padding:10,borderRadius:20,marginTop:10}">
          <text :style="{color:'white',fontWeight:'bold'}">ACCEPT JOB</text>
        </touchable-opacity>
        </view>
      </view>
      <touchable-opacity v-if="isClose" :on-press="open" :style="{backgroundColor:'grey',padding:10,borderRadius:20,marginTop:50,alignItems:'center'}">
            <text :style="{color:'white',fontWeight:'bold'}">open</text>
          </touchable-opacity> -->
      <!-- <text>hasd</text> -->
      <!-- <text>Lat {{coordinates.latitude}}</text> -->
        <!-- <text>Long {{coordinates.longitude}}</text> -->
  </view>
</template>

<script>
// :region="coordinates"
        //  :onRegionChange="onRegionChange"
// import getDirections from 'react-native-google-maps-directions';
// import {Text} from 'react-native';
// import { Divider } from 'react-native-';
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
    // this.deliveryJobRef.on('value', (snapshot) => {
    //   if (snapshot !== undefined) {
    //     this.deliveryJob = snapshot.val();
    //   }
    // });
    // this.pharmacyRef.on('value', (snapshot) => {
    //   if (snapshot !== undefined) {
    //     this.pharmacy = snapshot.val();
    //   }
    // });
    // this.makeMarkers();
  },
  props: {
    navigation: {
      type: Object,
    },
  },
    data() {
    return {
      is_accept_stage:false,
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
      dynamic_padding:0,
      duration:0,
      pharmacy_name:'',
        GOOGLE_MAPS_APIKEY: 'AIzaSyC8oXnYPjm2GihFIjDsFt9iwDfCflvcRos',
        PROVIDER_GOOGLE: PROVIDER_GOOGLE,
        customMap: mapDarkStyle,
        isClicked: false,
         deliveryJob: {},
            deliveryJobRef: null,
            pharmacy: {},
            pharmacyRef: null,
            markers: [{latitude: 13.70,
    longitude: 100.508},{latitude: 13.760502698226656,
    longitude: 100.50810555219532},{latitude: 13.75,
    longitude: 100.50}],
        marker1: {latitude: 13.760502698226656,
    longitude: 100.50810555219532},marker2: {latitude: 13.75,
    longitude: 100.50},
    // origin: {latitude: 13.760,
    // longitude: 100.48},
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
    // Text,
  },
  methods: {
    async setDeliveryJob() {
        await this.pharmacyRef.on('value', (snapshot) => {
      if (snapshot !== undefined) {
        this.pharmacy = snapshot.val();
      }
    });
       await this.deliveryJobRef.on('value', (snapshot) => {
        // const delivery = {};
        // snapshot.forEach((deliver) => {
        //   delivery[deliver.key] = deliver.val();
        // });
        // this.deliveryJob = delivery;
    //   if (snapshot !== undefined) {
        this.deliveryJob = snapshot.val();
        this.makeMarkers();
    //   }
    //   console.log(this.deliveryJob);
    //    this.makeMarkers();
    });
    },
    accept() {
      this.is_accept_stage = false;
      this.isPickUp = true;
      this.dynamic_padding = 130;
    },
    pick_up() {
      this.isPickUp = false;
      this.isDropOff = true;
    },
    setDistance(result) {
      this.distance = result.distance.toFixed(1);
      this.duration = result.duration.toFixed(1);
    },
    // changeLocation() {
    //   this.currentLocation = {
    //             latitude: 13.5,
    //             longitude: this.longitude,
    //             latitudeDelta: 0.0922,
    //             longitudeDelta: 0.0421
    //           ,
    //           }
    // },
      makeMarkers() {
        if (this.deliveryJob) {
            // console.log(this.deliveryJob);
            // const x = this.deliveryJob;
            // Object.entries(x).forEach((x)=>console.log(x));
            Object.entries(this.deliveryJob).forEach((key) => {
                // console.log(z);
                const [id, job] = key;
                // console.log(job);
                this.new_markers.push({
                    destination:{
                        latitude:job.toLocation.lat,
                        longitude:job.toLocation.lng
                    },
                    waypoints: [{
                        latitude:job.fromLocation.lat,
                        longitude:job.fromLocation.lng
                    }],
                    name: this.getName(job.pharmacyId),
                    pharmacyAddress:job.fromAddress,
                    customerAddress:job.toAddress
                });

                // this.new_markers.push({destination:{latitude:key[1].toLocation.lat,longitude:key[1].toLocation.lng},
                // waypoints:[{latitude:key[1].fromLocation.lat,longitude:key[1].fromLocation.lng}],name:this.getName(key[1].pharmacyId),
                // pharmacyAddress:key[1].fromAddress,customerAddress:key[1].toAddress});
            });
        }
      },
      getName(p) {
      let data = null;
      Object.entries(this.pharmacy).forEach((key) => {
        if (key[0] === p) {
          data = key[1].name;
        }
      });
      return data;
    },
    close() {this.isUseCard = false; this.isClose = true;},
     open() {this.isUseCard = true; this.isClose = false;},
    handleGetDirection(marker) {
        this.is_accept_stage = true;
      this.isUseCard = true;
    //   this.isPressForInfo = true;
    this.dynamic_padding = 260;
      this.isUseDirection = true;
      // this.isClicked = !this.isClicked;
      this.destination = marker.destination;
      this.waypoints = marker.waypoints;
      this.pharmacy = this.waypoints[0];
      this.pharmacy_name = marker.name
      // this.pharmacyAddress = marker.pharmacyAddress;
      // this.customerAddress = marker.customerAddress;
      this.pharmacyAddress = marker.pharmacyAddress.replace(',','').split(" ").slice(0,4).join(" ");
      this.customerAddress = marker.customerAddress.replace(',','').split(" ").slice(0,4).join(" ");
    // const data = {
    //    source: this.marker1,
    //   destination: this.marker2,
    //   params: [
    //     {
    //       key: "travelmode",
    //       value: "driving"        // may be "walking", "bicycling" or "transit" as well
    //     },
    //     {
    //       key: "dir_action",
    //       value: "navigate"       // this instantly initializes navigation using the given travel mode
    //     }
    //   ],
    //   waypoints: [
    //     {
    //       latitude: -33.8600025,
    //       longitude: 18.697452
    //     },
    //     {
    //       latitude: -33.8600026,
    //       longitude: 18.697453
    //     },
    //        {
    //       latitude: -33.8600036,
    //       longitude: 18.697493
    //     }
    //   ]
    // }
 
    // getDirections(data)
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
              // console.log(location);
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
.background {
    /* background-color: grey; */
    /* flex: 1; */
}
.accepting_stage{
  background-color: rgb(255, 255, 255);
  position: absolute;
  border-radius: 10;
  height: 250;
  width: 320;
  margin:25;
  /* padding: 50; */
  bottom: 20;
}
.container {
  /* width: 400; */
  /* height: 400; */
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
  /* width: 400; */
  /* height:  400; */
  /* padding: 50px; */
  /* flex: 1; */
}
.description{
  background-color: rgba(0, 0, 0,0.4);
  position: absolute;
  /* border-radius: 10; */
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
  height: 250;
  width: 100%;
  padding: 24;
  bottom: 0;
  /* margin-bottom: 100; */
}
.card{
  background-color: rgb(255, 255, 255);
  position: absolute;
  border-radius: 10;
  height: 160;
  width: 100%;
  padding: 20;
  bottom: 0;
  /* margin-bottom: 100; */
}
.toolbar{
  background-color: rgb(156, 153, 153);
  position: absolute;
  border-radius: 10;
  height: 150;
  width: 100%;
  padding: 24;
  top: 0;
  /* flex-direction: row; */
  justify-content:center;
  /* align-items: center; */
  /* flex-direction: row; */
  /* margin-bottom: 100; */
}
</style>
