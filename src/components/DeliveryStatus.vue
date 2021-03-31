<template>
  <view>
    <ProgressSteps
      v-if="current < 3"
      :activeStep="current"
    >
      <ProgressStep
        :removeBtnRow="true"
        v-for="(step,n) in steps"
        :key="n"
        :step="n+1"
        :label="step.name"
      >
      <!-- <view>
          <text> image </text>
      </view> -->
      </ProgressStep>
    </ProgressSteps>
    <ProgressSteps
      v-else
      :isComplete="true"
    >
      <ProgressStep :removeBtnRow="true" :step="0" label="Finding Driver"/>
      <ProgressStep :removeBtnRow="true" :step="1" label="Delivering"/>
      <ProgressStep :removeBtnRow="true" :step="2" label="Delivered"/>
    </ProgressSteps>
  </view>
</template>
<script>
import { ProgressSteps, ProgressStep } from 'react-native-progress-steps';

export default {
  components: {
    ProgressSteps,
    ProgressStep,
  },
  props: {
    current: {
      type: Number,
    },
  },
  name: 'Status',
  data() {
    return {
      steps: [
        { name: 'Finding Driver' },
        { name: 'Delivering' },
        { name: 'Delivered' },
      ],
    };
  },
  methods: {
  },
};
</script>
