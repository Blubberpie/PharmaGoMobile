<template >
    <!-- <text class="text-color-primary">BTS</text> -->
    <!-- <view> -->
        <!-- <view class='sign-up'> -->
        <!-- <text class='text'>Create Account</text> -->
        <!-- </view> -->
    <view class='container'>
        <text class='text'>Find your Job</text>
        <scroll-view :content-container-style="{contentContainer: {
        paddingVertical: 20
    }}">
        <view :style="{padding:10,backgroundColor:''}" v-for="(task, index) in tasks" :key="index">
            <touchable-opacity class='card'>
                <view :style="{alignItems:'flex-end', backgroundColor:''}">
                <text :style="{color:'#5b5b5b'}">123 km</text>
                </view>
            <view :style="{flexDirection: 'row',backgroundColor:''}">
            <image :style="{width: 40,height: 40,borderRadius: 25, marginRight:8}"
            :source="{uri: task.imageSrc}"/>
            <!-- <text>{{task.description}}</text> -->
            <view :style="{flexDirection: 'column',backgroundColor:''}">
            <text :style="{fontSize:16,fontWeight: 'bold',color:'#5b5b5b'}">{{task.title}}</text>
            <text :style="{color:'#5b5b5b'}">{{task.description}}</text>
            </view>
            </view>
            </touchable-opacity>
            <!-- <view :style="{flexDirection: 'row'}">
            <touchable-opacity :style="{ marginLeft:80}" :on-press="() => apply()">
      <image :style="{width: 43,height: 43,borderRadius: 35, marginRight: 8,
       marginBottom: 0,}"
            :source="{uri: 'https://i.pinimg.com/originals/b8/85/10/b88510e4e0a7c0457e16495880546429.png'}"/>
    </touchable-opacity>
    <touchable-opacity :style="{}" :on-press="() => getLocation(task.address)">
      <image :style="{width: 25,height: 25,borderRadius: 25,}"
            :source="{uri: 'https://i.pinimg.com/originals/f7/e0/45/f7e04570aedfd8a78f8e49e2eaded974.png'}"/>
    </touchable-opacity>
            </view> -->
            <!-- </view> -->
            <!-- <text>{{task.description}}</text> -->
        </view>
        </scroll-view>
    <!-- <button title="Register" color="#009999"/> -->
     <!-- <button title="You already have an account ?" color="#009999" class='text'/> -->
    </view>
    <!-- </view> -->
</template>
<script>
// import {Picker} from 'react-native';
import { Alert } from 'react-native';

export default {
    data() {
        return {
            tasks: [{
              'title' : 'A pharmacy',
               'imageSrc' : 'https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcT04s_fztbv3ncwe0O_x5tbAmgLZCsPKorMzw&usqp=CAU',
               'description' : 'High quality',
               'address' : 'Bangkok', 
            },
            {
              'title' : 'B pharmacy',
               'imageSrc' : 'https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcT04s_fztbv3ncwe0O_x5tbAmgLZCsPKorMzw&usqp=CAU',
               'description' : 'I love you so much I love you sodasdasd asda asd ads xdas asd',
               'address' : 'Bangkok', 
            },{
              'title' : 'C pharmacy',
              'imageSrc' : 'https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcT04s_fztbv3ncwe0O_x5tbAmgLZCsPKorMzw&usqp=CAU',
               'description' : 'Low quality',
               'address' : 'Bangkok', 
            },{
              'title' : 'D pharmacy',
              'imageSrc' : 'https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcT04s_fztbv3ncwe0O_x5tbAmgLZCsPKorMzw&usqp=CAU',
               'description' : 'Low quality',
               'location' : 'Bangkok', 
            },{
              'title' : 'E pharmacy',
              'imageSrc' : 'https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcT04s_fztbv3ncwe0O_x5tbAmgLZCsPKorMzw&usqp=CAU',
               'description' : 'Low quality',
               'address' : 'Bangkok', 
            },
            {
              'title' : 'F pharmacy',
              'imageSrc' : 'https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcT04s_fztbv3ncwe0O_x5tbAmgLZCsPKorMzw&usqp=CAU',
               'description' : 'Low quality',
               'address' : 'Bangkok', 
            },
            {
              'title' : 'G pharmacy',
              'imageSrc' : 'https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcT04s_fztbv3ncwe0O_x5tbAmgLZCsPKorMzw&usqp=CAU',
               'description' : 'Low quality',
               'address' : 'Bangkok', 
            }],
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
        }
    }
};
</script>
<style scoped>
.text{
    /* font-family: 'Franklin Gothic Medium', 'Arial Narrow', Arial, sans-serif; */
    color:#bdb8b8;
    font-size:  22px;
    font-weight: bold;
    padding-bottom: 50;
    margin-left: 30;
    /* padding-top: 200; */
    /* color: #009999; */
}
.container {
  /* background-color: red; */
  /* align-items: center; */
  /* justify-content: center; */
  background-color: white;
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
    width: 380;
    height: 120;
    padding: 10;
    background-color:#efefef;
    border-radius: 15;
    /* flex-direction: row; */
    /* align-items: center; */
}
</style>
