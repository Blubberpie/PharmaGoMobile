<template>
    <view class='container'>
        <view class='card'>
        <view :style="{alignItems:'center'}">
        <image  v-if="navigation.getParam('imageSrc')" :style="{width: 40,height: 40,borderRadius: 25, marginRight:8}"
            :source="{uri: navigation.getParam('imageSrc')}"/>
            <image v-else :style="{width: 50,height: 50,borderRadius: 25, marginRight:8}"
            :source="{uri: 'https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcT04s_fztbv3ncwe0O_x5tbAmgLZCsPKorMzw&usqp=CAU'}"/>
        <text class='text'>{{navigation.getParam('title')}}</text>
        <text class='text'>distance: {{navigation.getParam('distance')}} km</text>
        </view>
        <view>
        <text class='text' >Customer's address: {{navigation.getParam('customerAddress')}}</text>
        <text class='text'>Pharmacy's address: {{navigation.getParam('pharmacyAddress')}}</text>
        </view>
        <touchable-opacity :style="{ borderRadius:10,backgroundColor:'#21ADA8',padding:10,marginTop:30,alignItems:'center'}" :on-press="() => apply(navigation.getParam('this_index'))">
            <text :style="{color:'white',fontWeight:'bold'}">Apply for Job</text>
      <!-- <image :style="{width: 43,height: 43,borderRadius: 35, marginRight: 8,
       marginBottom: 0,}"
            :source="{uri: 'https://i.pinimg.com/originals/b8/85/10/b88510e4e0a7c0457e16495880546429.png'}"/> -->
    </touchable-opacity>
        </view>
    </view>
</template>

<script>
import { Alert } from 'react-native';
export default {
    props: {
        navigation: {
            type: Object
        }
    },
    methods:{
        apply(index) {
            Alert.alert("Your delivery assigned", 'Please pick up the prescription from pharmacy and delivery to customer');
            this.updateStatus(index);
            this.navigation.navigate('Home');
        },
        updateStatus(index) {
        // console.log(index);
        this.navigation.getParam('ref').transaction((snapshot) => {
            Object.entries(snapshot).forEach((key) => {
            if (key[0] === index) {
                this.navigation.getParam('ref').child(key[0]).update({
                status: 2,
                });
            }
            });
        });
        },
    }
}
</script>

<style scoped>
.text {
    color: #4b4747;
    font-size: 17;
    margin-top:15;
}
.container {
  /* background-color: red; */
  align-items: center;
  background-color: #87cfeb23;
  /* justify-content: center; */
  padding-top: 50;
  flex: 1;
}
.card {
    /* border-bottom-width: 1; */
    /* border-color: red; */
    /* width: 330; */
    /* height: 120; */
    /* justify-content: center; */
    /* margin-top: 100; */
    padding: 10;
    background-color:white;
    /* border-color: red; */
    /* color: red; */
    border-radius: 15;
    /* flex-direction: row; */
    /* align-items: center; */
}
</style>
