<template>
  <View>
    <Title>Manage Your Pending Prescriptions</Title>
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
        <Button title="Show Details" @press="showDetails(key)"/>
        <Button title="Accept" @press="approvePrescription(prescription.pharmacyId, key)"/>
        <Button title="Reject"/>
      </CardActions>
    </Card>
    <Portal>
      <Dialog :visible="detailsVisible" :onDismiss="hideDialog">
        <DialogTitle><Text>Proposed Medication</Text></DialogTitle>
        <DialogContent>
          <DTable>
            <DTHeader>
              <DTTitle>Drug</DTTitle>
              <DTTitle>Strength</DTTitle>
              <DTTitle>Frequency</DTTitle>
              <DTTitle numeric>Quantity</DTTitle>
            </DTHeader>
            <DTRow
              v-for="(medication, key) in selectedPrescription"
              :key="key"
            >
              <DTCell>{{ medication.name }}</DTCell>
              <DTCell>{{ medication.strength }}</DTCell>
              <DTCell>{{ medication.frequency }}</DTCell>
              <DTCell>{{ medication.quantity }}</DTCell>
            </DTRow>
            <!-- Doesnt work for now -->
            <DTPagination
              :page="currentTablePage"
              :numberOfPages="Math.ceil(selectedPrescription.length / itemsPerPage)"
              :onPageChange="(page) => setPage(page)"
            />
          </DTable>
        </DialogContent>
      </Dialog>
    </Portal>
  </View>
</template>

<script>
import firebase from 'firebase/app';
import 'firebase/database';
import { StyleSheet } from 'react-native';
import {
  Card,
  Portal,
  Dialog,
  Title,
  DataTable,
} from 'react-native-paper';

const database = firebase.database();
const styles = StyleSheet.create({
  cardAction: {
    justifyContent: 'space-between',
  },
  card: {
    margin: 5,
  },
});

const STATUS_PENDING = 1;
const STATUS_APPROVED = 2;

export default {
  components: {
    Card,
    CardActions: Card.Actions,
    CardTitle: Card.Title,
    Portal,
    Dialog,
    DialogTitle: Dialog.Title,
    DialogContent: Dialog.Content,
    Title,
    DTable: DataTable,
    DTHeader: DataTable.Header,
    DTTitle: DataTable.Title,
    DTRow: DataTable.Row,
    DTCell: DataTable.Cell,
    DTPagination: DataTable.Pagination,
  },
  data() {
    return {
      prescriptions: {},
      customerPrescriptionsRef: null,
      // prescriptions: {
      //   '-MW3sZcJlx4xb-liGNKS': {
      //     medication: [{
      //       frequency: '1',
      //       name: '1',
      //       quantity: '1',
      //       strength: '1',
      //     }],
      //     patientId: 'eSfIbpVKbPZVGVVaYeGdZ3ZicsV2',
      //     pharmacyId: {
      //       pharmacyID: '-MVk4z6wEWaamUVbFAwV',
      //     },
      //     pharmacyName: 'pharmacy1',
      //     prescriberEmail: 'asd@gasd.aaa',
      //     prescriberName: 'asdsad',
      //     prescriberPhone: '123123',
      //     status: 1,
      //   },
      //   '-MKwiewifoi': {
      //     medication: [
      //       {
      //         frequency: '123234',
      //         name: '1243234',
      //         quantity: '2343421',
      //         strength: '3242341',
      //       },
      //       {
      //         frequency: '123sadf234',
      //         name: '12sdaf43234',
      //         quantity: '23sf43421',
      //         strength: '324f2341',
      //       },
      //     ],
      //     patientId: 'eSfIbpVKbPZVGVVaYeGdZ3ZicsV2',
      //     pharmacyId: {
      //       pharmacyID: '-MVk4z6wEWaamUVbFAwV',
      //     },
      //     pharmacyName: 'pharmacy1',
      //     prescriberEmail: 'asd@gasd.aaa',
      //     prescriberName: 'asdsad',
      //     prescriberPhone: '123123',
      //     status: 1,
      //   },
      // },
      styles,
      detailsVisible: false,
      selectedPrescription: [],
      currentTablePage: 0,
      itemsPerPage: 4,
    };
  },
  created() {
    this.customerPrescriptionsRef = database.ref('/prescriptions/eSfIbpVKbPZVGVVaYeGdZ3ZicsV2');
    this.customerPrescriptionsRef.orderByChild('status').equalTo(STATUS_PENDING)
      .on('value', (prescriptionsSnap) => {
        const ps = {};
        prescriptionsSnap.forEach((prescription) => {
          ps[prescription.key] = prescription.val();
        });
        this.prescriptions = ps;
      });
  },
  mounted() {
  },
  methods: {
    showDetails(key) {
      this.detailsVisible = true;
      this.selectedPrescription = this.prescriptions[key].medication;
    },
    hideDialog() {
      this.detailsVisible = false;
    },
    setPage(number) {
      this.currentTablePage = number;
    },
    async approvePrescription(pharmacyId, prescriptionId) {
      this.detailsVisible = false;
      const pharmacyRef = database.ref(`/registered-pharmacies/${pharmacyId}`);
      const deliveryJobsRef = database.ref('/deliveryJobs');

      // let done = false;

      await pharmacyRef.once('value', (pharmacySnap) => {
        const pharmacyLocation = pharmacySnap.val().location;
        const pharmacyAddress = pharmacySnap.val().address;
        const customerAddress = 'Thung Phaya Thai, 10400, Bangkok, Ratchathewi, Thailand';
        const customerLocation = {
          lat: 13.7605358,
          lng: 100.5267991,
        };

        if (pharmacyLocation && pharmacyAddress) {
          const newDeliveryJob = {
            pharmacyId,
            customerId: 'eSfIbpVKbPZVGVVaYeGdZ3ZicsV2',
            fromLocation: pharmacyLocation,
            fromAddress: pharmacyAddress,
            toLocation: customerLocation,
            toAddress: customerAddress,
            status: 1, // 1 = unassigned
          };
          deliveryJobsRef.push(newDeliveryJob)
            .then(() => {
              this.customerPrescriptionsRef.child(prescriptionId).update({
                status: STATUS_APPROVED,
              });
              console.log('success');
              // done = true;
            })
            .catch((error) => {
              console.log(error);
            });
        } else {
          console.log('Couldn\'t communicate with the server');
        }
      });
    },
  },
};
</script>
