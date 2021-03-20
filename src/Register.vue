<template>
    <!-- <text class="text-color-primary">BTS</text> -->
    <!-- <view> -->
        <!-- <view class='sign-up'> -->
        <!-- <text class='text'>Create Account</text> -->
        <!-- </view> -->
    <view class='container'>
        <text class='text'>Create Account</text>
        <text :style="{color:'gray'}">Username</text>
      <text-input class="input" auto-capitalize="none"
        :style="{width:300, height:40,padding: 10, borderColor: '#D3D3D3', borderWidth: 1,margin:10}"
        v-model="username"
      />
      <text :style="{color:'gray'}">Email</text>
      <text-input class="input" auto-capitalize="none"
       :style="{width:300, height:40,padding: 10, borderColor: '#D3D3D3', borderWidth: 1,margin:10}"
        v-model="email"
      />
      <text :style="{color:'gray'}">Password</text>
      <text-input class="input" auto-capitalize="none" secure-text-entry
        :style="{width:300, height:40,padding: 10, borderColor: '#D3D3D3', borderWidth: 1,margin:10}"
        v-model="password"
      />
      <text :style="{color:'gray'}">Type: Customer, Driver, Pharmacy</text>
      <text-input class="input"
        :style="{width:300, height:40,padding: 10, borderColor: '#D3D3D3', borderWidth: 1,margin:10}"
        v-model="role"
      />
      <touchable-opacity :style="{width:300, height:40,padding: 10, alignItems: 'center',backgroundColor: '#009999',
      borderRadius: 10,margin:10}">
        <text :style="{color: 'white'}">Register</text>
    </touchable-opacity>
    <view :style="{flexDirection: 'row',justifyContent: 'center',paddingTop: 50}">
    <text :style="{fontSize:15}">You already have an </text>
    <touchable-opacity >
        <text :style="{color: 'purple',fontSize:15}" >account
    </touchable-opacity>
    <text>?</text>
    </view>
    <!-- <button title="Register" color="#009999"/> -->
     <!-- <button title="You already have an account ?" color="#009999" class='text'/> -->
    </view>
    <!-- </view> -->
</template>
<script>
// import {Picker} from 'react-native';

export default {
    data() {
        return {
            username: '',
            email: '',
            password: '',
            role: '',
        }
    }
};
</script>
<style scoped>
.text{
    /* font-family: Arial, Helvetica, sans-serif; */
    font-size:  22px;
    font-weight: bold;
    padding-bottom: 100;
    /* padding-top: 200; */
    /* color: #009999; */
}
.container {
  /* background-color: red; */
  /* align-items: center; */
  /* justify-content: center; */
  padding-top: 100;
  flex: 1;
}
.input {
    border-radius: 10px;
}
.sign-up {
    align-items: center;
    /* background-color:red; */
    /* margin-top: 100; */
}
</style>
