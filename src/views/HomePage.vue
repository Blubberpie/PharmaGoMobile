<template>
  <view class="container">
    <text class="text-color-primary">Home Page</text>
    <button title="Press me!" @press="exclaim" />
    <button title="Map Page" @press="toMapPage" />
    <button title="Pending Prescriptions" @press="toPendingPrescriptions" />
  </view>
</template>

<script>

export default {
  data() {
    return {
      message: '',
    };
  },
  mounted() {
    this.message = 'Hello World'; // testing mounted
  },
  props: {
    navigation: {
      type: Object,
    },
  },
  methods: {
    exclaim() {
      this.message += '!';
      // eslint-disable-next-line no-alert
      alert(this.message);
    },
    toMapPage() {
      this.navigation.navigate('CustomerMap');
    },
    toPendingPrescriptions() {
      this.navigation.navigate('PendingPrescriptions');
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
