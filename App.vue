<template>
  <view class="container">
    <text class="text-color-primary">My Vue Native App</text>
    <button title="Press me!" @press="exclaim" />
  </view>
</template>

<script>
export default {
  data() {
    return {
      message: ""
    };
  },
  mounted() {
    this.message = "Hello World"; // testing mounted
  },
  methods: {
    exclaim() {
      this.message += "!";
      alert(this.message);
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
