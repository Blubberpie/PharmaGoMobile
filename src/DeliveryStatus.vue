<template>
  <view>
    <ProgressSteps :activeStep="current"
    :isComplete="complete"
    >
        <ProgressStep
        :removeBtnRow="true"
        v-for="(step,n) in steps"
        :key="n"
        :step="n+1"
        :label="step.name"
        :complete="stepComplete(n+1)"
        >
        <!-- <view>
            <text> image </text>
        </view> -->
        </ProgressStep>
        <!-- <Alert title="Thank you" message="your presciption has been delivered"> -->
    </ProgressSteps>
  </view>
</template>
<script>
import { ProgressSteps, ProgressStep } from 'react-native-progress-steps';
import { Alert } from 'react-native';

export default {
  components: {
    ProgressSteps,
    ProgressStep,
    Alert
  },
  // props: {
  //   current: {
  //     type: Number,
  //   },
    // navigation: {
    //   type: Object
    // }
  
  // },
  name: 'Status',
  data() {
    return {
      current: 0,
      complete: false,
      
      steps: [
        { name: 'Finding Driver' },
        { name: 'Delivering' },
        { name: 'Delivered' },
      ],
    };
  },
  methods: {
    stepComplete(step) {
      if (this.current > step+1) {
        this.complete=true;
        Alert.alert("Thank you for ordering, your presciption has been delivered")
        // this.navigation.navigate('Home')
      }
      // return this.current > 2;
    },
  },
}
</script>