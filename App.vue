<template>
  <view class="container">
    <driver-job/>
    <!-- <text class="text-color-primary">My Vue Native App</text> -->
    <!-- <button title="Press me!" @press="exclaim" /> -->
  </view>
</template>

<script>
import DriverJob from './src/components/DriverJob';
// import DriverJob from './src/components/DriverJob.vue';
import './src/plugins/firebase';

export default {
  components:{ DriverJob},
  data() {
    return {
      message: '',
    };
  },
  mounted() {
    this.message = 'Hello World'; // testing mounted
  },
  methods: {
    exclaim() {
      this.message += '!';
      // eslint-disable-next-line no-alert
      alert(this.message);
    },
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
