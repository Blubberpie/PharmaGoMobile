<template>
  <view class="container">
    <status/>
    <!-- <text class="text-color-primary">My Vue Native App</text>
    <button title="Press me!" @press="exclaim" /> -->
  </view>
</template>

<script>
import status from './src/DeliveryStatus'

export default {
  components: { status },
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
