<template >
    <view class='background'>
      <text>
      {{computeDistance()}}</text>
        <!-- <text>{{deliveryJob}}</text> -->
        <text class='text'>◌ Jobs</text>
        <!-- <text>Lat {{latitude}}</text>
        <text>Long {{longitude}}</text> -->
        <view :style="{alignItems:'flex-end', marginRight:30,marginTop:10}">
        <touchable-opacity :style="{}" :on-press="sortBy">
            <text :style="{color:'#5b5b5b',alignItems:'flex-end'}">sort by distance</text>
        </touchable-opacity>    
        </view>
        <scroll-view :content-container-style="{contentContainer: {
        paddingVertical: 20,
    }}">
        <view :style="{padding:10,backgroundColor:'',alignItems:'center'}" v-for="(post, index) in inRange" :key="index">
            <touchable-opacity class='card' :on-press="() => onPressButton(post,index)">
                <view :style="{alignItems:'flex-end', backgroundColor:''}">
                <text :style="{color:'#5b5b5b',fontSize:13}">{{post.distance}} km</text>
                </view>
            <view :style="{flexDirection: 'row',backgroundColor:''}">
            <image v-if="post.logo" :style="{width: 40,height: 40,borderRadius: 25, marginRight:8}"
            :source="{uri: post.imageSrc}"/>
            <image v-else :style="{width: 40,height: 40,borderRadius: 25, marginRight:8}"
            :source="{uri: 'https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcT04s_fztbv3ncwe0O_x5tbAmgLZCsPKorMzw&usqp=CAU'}"/>
            <!-- <text>{{task.description}}</text> -->
            <view :style="{flexDirection: 'column',backgroundColor:''}">
            <text :style="{fontSize:16,fontWeight: 'bold',color:'#5b5b5b'}">{{getName(post.pharmacyId)}}</text>
            <text :style="{color:'#5b5b5b'}">{{getDescription(post.pharmacyId)}}</text>
            </view>
            </view>
            </touchable-opacity>
        </view>
        </scroll-view>
        <text :style="{color:'#5b5b5b', marginLeft:50}">Where you are at</text>
        <text :style="{color:'#5b5b5b', marginLeft:50}">Lat {{latitude}}</text>
        <text :style="{color:'#5b5b5b', marginLeft:50}">Long {{longitude}}</text>
    </view>
</template>
<script>
import { Alert } from 'react-native';
import firebase from 'firebase/app';
import 'firebase/auth';
import 'firebase/database';
import * as Location from 'expo-location';
import * as Permissions from 'expo-permissions';

const database = firebase.database();
export default {
    props: {
        navigation: {
            type: Object,
        }
    },
    created() {
    this.deliveryJobRef = database.ref('deliveryJobs');
    this.pharmacyRef = database.ref('registered-pharmacies');
    this.getLocation();
    
  },
  mounted() {
    this.deliveryJobRef.on('value', (snapshot) => {
      if (snapshot !== undefined) {
        this.deliveryJob = snapshot.val();
      }
    });
    this.pharmacyRef.on('value', (snapshot) => {
      if (snapshot !== undefined) {
        this.pharmacy = snapshot.val();
      }
    });
  },
    data() {
        return {
            location: {},
            latitude: 0,
            longitude: 0,
            errorMessage: '',
            deliveryJob: {},
            deliveryJobRef: null,
            pharmacy: {},
            pharmacyRef: null,
        }
    },
    methods: {
        getLocation(address) {
            Alert.alert(
                'Address',
                "The address of customer and pharmacy ",
                [
                    {text: 'Customer', onPress: () => Alert.alert("Customer's address", address)},
                    {text: 'Pharmacy', onPress: () => Alert.alert("Pharmacy's address", address)},
                    {text: 'Cancel', onPress: () => console.log('Cancel Pressed'), style: 'cancel'},
                ],
                { cancelable: false }
            );
        },
        apply() {
            Alert.alert("Your delivery assigned", 'Please pick up the prescription from pharmacy and delivery to customer')
        },
        onPressButton(post, index) {
                this.navigation.navigate('another',{
                    title: this.getName(post.pharmacyId),
                    imageSrc: post.imageSrc,
                    description: this.getDescription(post.pharmacyId),
                    pharmacyAddress: post.fromAddress,
                    customerAddress: post.toAddress,
                    distance: post.distance,
                    this_index: index,
                    ref: this.deliveryJobRef
                });
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
    
    getDescription(p) {
      let data = null;
      Object.entries(this.pharmacy).forEach((key) => {
        if (key[0] === p) {
          data = key[1].description;
        }
      });
      return data;
    },
    computeDistance() {
      Object.entries(this.deliveryJob).forEach((key) => {
        const storedDistance = this.CalculateDistance(key[1].fromLocation.lat,
          key[1].fromLocation.lng);
        key[1].distance = storedDistance;
      });
    },
    getLocation: function() {
      Permissions.askAsync(Permissions.LOCATION)
        .then(status => {
          if (!status.granted) {
            this.errorMessage = "Permission to access location was denied";
          } else if (status.granted) {
            Location.getCurrentPositionAsync({}).then(location => {
              this.location = location;
              this.latitude = location.coords.latitude
              this.longitude = location.coords.longitude
              this.errorMessage = "";
              this.computeDistance();
            });
          }
        })
        .catch(err => {
          console.log(err);
        });
    }, 
    CalculateDistance(lat, lng) {
      const R = 6371;
      const lat1 = lat * (Math.PI / 180);
      const lat2 = this.latitude * (Math.PI / 180);
      const deltalat = (lat - this.latitude) * (Math.PI / 180);
      const deltalng = (lng - this.longitude) * (Math.PI / 180);

      const a = Math.sin(deltalat / 2) * Math.sin(deltalat / 2)
                + Math.cos(lat1) * Math.cos(lat2)
                * Math.sin(deltalng / 2) * Math.sin(deltalng / 2);
      const c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1 - a));
      const d = R * c;
      return d.toFixed(1);
    },
    sortBy() {
      this.deliveryJob = this.sortByDistance;
    },
    },
    computed: {
        sortByDistance() {
            return Object.fromEntries(Object.entries(this.deliveryJob)
        .sort((a, b) => (a[1].distance < b[1].distance ? -1 : 1)));
    },
    inRange() {
      return Object.fromEntries(Object.entries(this.deliveryJob)
        .filter(s => s[1].status === 1));
    },
    }
};
</script>
<style scoped>
.background {
  background-color: #87cfeb23;
   flex: 1;
}
.text{
    /* font-family: 'Franklin Gothic Medium', 'Arial Narrow', Arial, sans-serif; */
    color:#4b4747;
    font-size:  24px;
    font-weight: bold;
    margin-left: 50;
    margin-top: 50;
    /* padding-bottom: 50; */
    /* margin-left: 30; */
    /* padding-top: 200; */
    /* color: #009999; */
}
.container {
  align-items: center;
  /* justify-content: center; */
  background-color: blue;
  padding-top: 100;
  /* flex: 1; */
}
.input {
    border-radius: 10px;
}
.sign-up {
    align-items: center;
    /* background-color:red; */
    /* margin-top: 100; */
}
.card {
    /* border-bottom-width: 1; */
    /* border-color: red; */
    width: 330;
    height: 120;
    padding: 10;
    background-color:white;
    /* border-color: red; */
    /* color: red; */
    border-radius: 15;
    /* flex-direction: row; */
    /* align-items: center; */
}
</style>

