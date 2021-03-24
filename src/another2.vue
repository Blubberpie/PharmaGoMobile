<template>
    <!-- <text class="text-color-primary">BTS</text> -->
    <!-- <view> -->
        <!-- <view class='sign-up'> -->
        <!-- <text class='text'>Create Account</text> -->
        <!-- </view> -->
    <view class='container'>
        <text class='text'>Create Account</text>
        <text :style="{color:'gray' }">Username</text>
      <text-input class="input" auto-capitalize="none" :error="{emailError}" :error-text="{emailError}"
        :style="[!usernameError ? {width:300, height:40,padding: 10,  borderColor: '#D3D3D3',borderWidth: 1,margin:10}: {width:300, height:40,padding: 10,  borderColor: 'red',borderWidth: 1,margin:10}]"
        v-model="username"
      />
       <text :style="{color:'red',marginLeft:15,fontSize:13}">{{usernameError}}</text>
      <text :style="{color:'gray'}">Email</text>
      <text-input class="input" auto-capitalize="none" auto-complete-type="email"
        text-content-type="emailAddress" 
        keyboard-type="email-address" :error="{emailError}" :error-text="{emailError}"
       :style="[!emailError ? {width:300, height:40,padding: 10,  borderColor: '#D3D3D3',borderWidth: 1,margin:10}: {width:300, height:40,padding: 10,  borderColor: 'red',borderWidth: 1,margin:10}]"
        v-model="email"
      />
        <text :style="{color:'red',marginLeft:15,fontSize:13}">{{emailError}}</text>
      <text :style="{color:'gray'}">Password</text>
      <text-input class="input" auto-capitalize="none" secure-text-entry
        :style="[!passwordError ? {width:300, height:40,padding: 10,  borderColor: '#D3D3D3',borderWidth: 1,margin:10}: {width:300, height:40,padding: 10,  borderColor: 'red',borderWidth: 1,margin:10}]"
        v-model="password"
      />
      <text :style="{color:'red',marginLeft:15,fontSize:13}">{{passwordError}}</text>
      <text :style="{color:'gray'}">Type: Customer, Driver, Pharmacy</text>
      <text-input class="input"
        :style="{width:300, height:40,padding: 10, borderColor: '#D3D3D3', borderWidth: 1,margin:10}"
        v-model="role"
      />
      <touchable-opacity :on-press="onClick" :style="{width:300, height:40,padding: 10, alignItems: 'center',backgroundColor: '#009999',
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
import { Alert } from 'react-native';
import firebase from 'firebase/app';
import 'firebase/auth';

export default {
    data() {
        return {
            username: '',
            email: '',
            password: '',
            role: '',
            usernameError: '',
            emailError: '',
            passwordError: '',
        }
    },
    methods: {
        onClick() {
            if (this.username.length <= 5) {
                this.usernameError = 'Must be longer than 5 characters'
            }
            else this.usernameError= '';
            if (!/[a-zA-z0-9._]+@[a-zA-Z]+\.[a-zA-z]{2,4}/.test(this.email)) {
                this.emailError = 'Invalid email';
            }
            else this.emailError= '';
            if (this.password.length <= 5) {
                this.passwordError = 'Must be longer than 5 characters'
            }
            else this.passwordError= '';
            if (this.role === '') {
                this.role = 'Customer';
            }
            if (this.usernameError === '' && this.emailError === '' && this.passwordError === '') {
                Alert.alert("Login successfully");
                this.SignUp();
                // console.log('dededed');
            }
        },
       async SignUp() {
        try {
            firebase.auth().createUserWithEmailAndPassword(this.email, this.password).then(
            (cred) => { console.log(cred); },
            );
            console.log('hi');
            // this.$router.replace({ name: 'Login' });
        } catch (e) {
            console.log(e);
        }
    },
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
.input:invalid {
   border-color: red;
}
.sign-up {
    align-items: center;
    /* background-color:red; */
    /* margin-top: 100; */
}
</style>
