<template>
  <view class="container">
    <!-- <register/> -->
    <driver />
    <!-- <text class="text-color-primary">My Vue Native App</text> -->
    <!-- <button title="Press me!" @press="exclaim" /> -->
     <!-- <touchable-opacity :on-press="onPressButton">
       <text>supp</text> -->
      <!-- <image
          :style="{width: 50, height: 50}"
          :source="{uri: 'https://i.pinimg.com/originals/27/bd/15/27bd15835ca9bde7473df37e02b3865e.png'}"
        /> -->
    <!-- </touchable-opacity> -->
  </view>
</template>

<script>
import register from './src/Register'
import driver from './src/DriverJob';

export default {
  components: {  register, driver},
  data() {
     return {
      message: "",
      value: false,
    };
  },
  mounted() {
    this.message = "Hello BOSS"; // testing mounted
  },
  methods: {
    exclaim() {
      this.message += "!";
      alert(this.message);
    },
     onPressButton: function() {
                alert('Clicked Image')
            }
  },
};
</script>

<style>
.container {
  background-color: white;
  align-items: center;
  justify-content: center;
  flex: 1;
}
.text-color-primary {
  color: blue;
}
</style>
