<template>
  <PortalHost>
    <!-- <ListItem
      v-for="(prescription, key) in prescriptions"
      :key="key"
      :title="prescription.pharmacyName"
      :titleStyle="styles.title"
    /> -->
    <Card
      v-for="(prescription, key) in prescriptions"
      :key="key"
      :style="styles.card"
      :elevation="5"
    >
      <CardTitle
        :title="prescription.pharmacyName"
      />
      <CardActions :style="styles.cardAction">
        <Button mode="contained" dark color="blue" :onPress="showDetails(key)">
          <Text>Show Details</Text>
        </Button>
        <Button mode="contained" dark color="green">
          <Text>Accept</Text>
        </Button>
        <Button mode="contained" dark color="red">
          <Text>Reject</Text>
        </Button>
      </CardActions>
    </Card>
    <Portal>
      <Dialog :visible="detailsVisible" :onDismiss="hideDialog">
        <DialogTitle><Text>asdfasdf</Text></DialogTitle>
        <DialogContent>
          <Text>asdfdfsafsda</Text>
        </DialogContent>
        <DialogActions>
          <Button onPress={hideDialog}><Text>Done</Text></Button>
        </DialogActions>
      </Dialog>
    </Portal>
  </PortalHost>
</template>

<script>
// import firebase from 'firebase/app';
// import 'firebase/database';
import { StyleSheet } from 'react-native';
import {
  Button,
  Card,
  Portal,
  Dialog,
} from 'react-native-paper';

// const database = firebase.database();
const styles = StyleSheet.create({
  container: {
    ...StyleSheet.absoluteFillObject,
    justifyContent: 'flex-start',
    alignItems: 'center',
  },
  title: {
    fontWeight: 'bold',
    fontSize: 17,
  },
  cardAction: {
    justifyContent: 'space-between',
  },
  card: {
    margin: 5,
  },
});

export default {
  components: {
    Button,
    Card,
    CardActions: Card.Actions,
    CardTitle: Card.Title,
    Portal,
    PortalHost: Portal.Host,
    Dialog,
    DialogTitle: Dialog.Title,
    DialogContent: Dialog.Content,
    DialogActions: Dialog.Actions,
  },
  data() {
    return {
      prescriptions: {
        '-MW3sZcJlx4xb-liGNKS': {
          medication: [{
            frequency: '1',
            name: '1',
            quantity: '1',
            strength: '1',
          }],
          patientId: 'eSfIbpVKbPZVGVVaYeGdZ3ZicsV2',
          pharmacyId: {
            pharmacyID: '-MVk4z6wEWaamUVbFAwV',
          },
          pharmacyName: 'pharmacy1',
          prescriberEmail: 'asd@gasd.aaa',
          prescriberName: 'asdsad',
          prescriberPhone: '123123',
          status: 1,
        },
        '-MKwiewifoi': {
          medication: [{
            frequency: '123234',
            name: '1243234',
            quantity: '2343421',
            strength: '3242341',
          }],
          patientId: 'eSfIbpVKbPZVGVVaYeGdZ3ZicsV2',
          pharmacyId: {
            pharmacyID: '-MVk4z6wEWaamUVbFAwV',
          },
          pharmacyName: 'pharmacy1',
          prescriberEmail: 'asd@gasd.aaa',
          prescriberName: 'asdsad',
          prescriberPhone: '123123',
          status: 1,
        },
      },
      styles,
      detailsVisible: false,
    };
  },
  created() {
  },
  mounted() {
  },
  methods: {
    showDetails(key) {
      this.detailsVisible = true;
      console.log(key);
    },
    hideDialog() {
      this.detailsVisible = false;
    },
  },
};
</script>
