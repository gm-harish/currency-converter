<template>
<div>
 <v-toolbar dense>
      <v-toolbar-title>Currency Convertor</v-toolbar-title>
      <v-spacer></v-spacer>
     <div> last Fetched Conversion rates at  {{timestamp}}</div>
     <v-btn icon @click="fetchCurrencyConversionData">
        <v-icon>mdi-cached</v-icon>
      </v-btn>
    </v-toolbar>
  <v-form v-model="valid">
    <v-container>
      <v-row>
        <v-col
          cols="12"
          md="4"
        >
          <v-text-field
            :value="eurValue"
            :type="'number'"
            label="EUR"
            @input="eurValueChanged($event, 'EUR')"
          ></v-text-field>
        </v-col>

        <v-col
          cols="12"
          md="4"
        >
          <v-text-field 
            :value="gbpValue"
            label="GBP"
            @input="eurValueChanged($event, 'GBP')"
          ></v-text-field>
        </v-col>

        <v-col
          cols="12"
          md="4"
        >
          <v-text-field
            :value="inrValue"
            label="INR"
            @input="eurValueChanged($event, 'INR')"
          ></v-text-field>
        </v-col>
        <v-col
          cols="12"
          md="4"
        >
          <v-text-field
            :value="usdValue"
            label="USD"
            @input="eurValueChanged($event, 'USD')"
          ></v-text-field>
        </v-col>
      </v-row>
    </v-container>
  </v-form>
  </div>
</template>
<script lang="ts">
import {Component, Vue} from 'vue-property-decorator';
import { VForm, VContainer, VRow,VCol, VTextField, VToolbar, VSpacer, VBtn, VIcon } from 'vuetify/lib';
import axios from 'axios';
import moment from 'moment';

@Component({
  components: {
    VForm,
    VContainer,
    VRow,
    VCol,
    VTextField,
    VToolbar,
    VSpacer,
    VBtn,
    VIcon
  },
})

export default class VwapStatsPane extends Vue {
  private valid = false;
  private eurValue: number | null = 1;
  private conversionMap: any= {
      GBP: 1,
      INR: 1,
      USD: 1,
      EUR: 1,
  }
  private timestamp: any = '';

  private get inrValue() {
    return this.eurValue ? this.eurValue * this.conversionMap.INR : null;
  }

  private get usdValue() {
    return this.eurValue? this.eurValue * this.conversionMap.USD : null;
  }

  private get gbpValue() {
    return this.eurValue ? this.eurValue * this.conversionMap.GBP: null;
  }

  private eurValueChanged(value: any, currency: string) {
    const parsedValue = parseInt(value);
    this.eurValue = parsedValue? parsedValue/this.conversionMap[currency]: null;
  }

  mounted() {
   this.fetchCurrencyConversionData();
   }

  private async  fetchCurrencyConversionData() {
    try {
      const {data} = await axios.get('http://data.fixer.io/api/latest?access_key=3a82e4b662de26098e211dfb00130f86&symbols=USD,INR,GBP');
      Object.assign(this.conversionMap, data.rates);
      this.timestamp = moment().format('MMMM Do YYYY, h:mm:ss a');
    } catch(err) {
      console.error(err);
    }
  }
}
</script>
