<template>
  <ScrollView>
    <Card
      :styles="styles.card"
      v-for="(job, key) in deliveryJobs"
      :key="key"
      :elevation="5"
    >
      <CardTitle :title="'Delivery ID: ' + key"/>
      <CardContent>
        <DeliveryStatus :current="job.status"/>
      </CardContent>
      <Divider/>
    </Card>
  </ScrollView>
</template>
<script>
import firebase from 'firebase/app';
import 'firebase/database';
import { StyleSheet, ScrollView } from 'react-native';
import { Card, Divider } from 'react-native-paper';

import DeliveryStatus from '../components/DeliveryStatus.vue';

const styles = StyleSheet.create({
  card: {
    margin: 5,
    height: 400,
    justifyContent: 'center',
  },
});

const database = firebase.database();

export default {
  components: {
    DeliveryStatus,
    Card,
    CardTitle: Card.Title,
    CardContent: Card.Content,
    ScrollView,
    Divider,
  },
  name: 'Statuses',
  data() {
    return {
      deliveryJobRef: null,
      deliveryJobs: {},
      uid: '',
      styles,
    };
  },
  created() {
    this.deliveryJobRef = database.ref('deliveryJobs');
    this.loadData();
  },
  methods: {
    async loadData() {
      this.uid = firebase.auth().currentUser.uid;
      await this.deliveryJobRef.once('value', (jobsSnap) => {
        const jobs = {};
        jobsSnap.forEach((job) => {
          if (job.val().customerId === this.uid) {
            jobs[job.key] = job.val();
          }
        });
        this.deliveryJobs = jobs;
      });
    },
  },
};
</script>
