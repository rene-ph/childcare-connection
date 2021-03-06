<template lang="html">
  <v-card flat>
    <hr>
    <v-card-title primary-title class="title">
      Financial Information
    </v-card-title>
    <hr>
    <v-card-text>
      <v-card flat>
        <v-toolbar color="secondary" dense dark>
          <v-toolbar-title>Primary Applicant</v-toolbar-title>
        </v-toolbar>
        <v-card-title>
          <v-spacer></v-spacer>
          <v-btn color="primary" outline @click="addIncome(true)" round outline small>
            <v-icon left>fas fa-plus-circle</v-icon>
            Add Applicant Income
          </v-btn>
        </v-card-title>
        <v-card-text>
          <v-card color="info" v-if="this.applicant.income" flat>
            <v-card-text>
              <v-layout row wrap class="subheading text-xs-left">
                <v-flex xs5>
                  Income Type
                </v-flex>
                <v-flex xs2 offset-xs1>
                  Frequency
                </v-flex>
                <v-flex xs2 offset-xs1>
                  Annual Total
                </v-flex>
              </v-layout>
            </v-card-text>
          </v-card>
          <v-card v-for="(record, index) in this.applicant.income" flat :key='index' :color="getCardColor(index)">
            <v-card-text>
              <v-layout row wrap class="subheading">
                <v-flex xs5>
                  {{record.type}}
                </v-flex>
                <v-flex xs2 offset-xs1>
                  {{record.frequency}}
                </v-flex>
                <v-flex xs2 offset-xs1>
                  ${{record.total}}
                </v-flex>
                <v-flex xs1 class="text-xs-right">
                  <v-btn color="primary" icon outline small @click="confirmDelete('app', index)">
                    <v-icon small>fas fa-trash</v-icon>
                  </v-btn>
                </v-flex>
              </v-layout>
            </v-card-text>
          </v-card>
        </v-card-text>
      </v-card>
      <v-card flat v-if="coapplicant.firstName">
        <v-toolbar color="secondary" dense dark>
          <v-toolbar-title>Co-Applicant</v-toolbar-title>
        </v-toolbar>
        <v-card-title primary-title>
          <v-spacer></v-spacer>
          <v-btn color="primary" outline @click="addIncome(false)" round outline small>
            <v-icon left>fas fa-plus-circle</v-icon>
            Add Co-Applicant Income
          </v-btn>
        </v-card-title>
        <v-card-text>
          <v-card color="info" v-if="this.coapplicant.income" flat>
            <v-card-text>
              <v-layout row wrap class="subheading text-xs-left">
                <v-flex xs5>
                  Income Type
                </v-flex>
                <v-flex xs2 offset-xs1>
                  Frequency
                </v-flex>
                <v-flex xs2 offset-xs1>
                  Annual Total
                </v-flex>
              </v-layout>
            </v-card-text>
          </v-card>
          <v-card v-for="(record, index) in this.coapplicant.income" flat :key='index' :color="getCardColor(index)">
            <v-card-text>
              <v-layout row wrap class="subheading">
                <v-flex xs5>
                  {{record.type}}
                </v-flex>
                <v-flex xs2 offset-xs1>
                  {{record.frequency}}
                </v-flex>
                <v-flex xs2 offset-xs1>
                  ${{record.total}}
                </v-flex>
                <v-flex xs1 class="text-xs-right">
                  <v-btn color="primary" icon outline small @click="confirmDelete('coapp', index)">
                    <v-icon small>fas fa-trash</v-icon>
                  </v-btn>
                </v-flex>
              </v-layout>
            </v-card-text>
          </v-card>
        </v-card-text>
      </v-card>
    </v-card-text>
    <hr>
    <v-layout row wrap>
      <v-flex xs1>
        <slot name="prev"></slot>
      </v-flex>
      <v-flex xs1 offset-xs8>
        <slot name="skip"></slot>
      </v-flex>
      <v-flex xs1 offset-xs1>
        <slot name="next"></slot>
      </v-flex>
    </v-layout>
    <v-dialog
      v-model="addIncomeDialog"
      persistent :overlay="false"
      max-width="500px"
      transition="dialog-transition"
    >
      <v-card>
        <v-toolbar color="primary" dense dark>
          <v-toolbar-title>Add Income for {{addIncomePerson ? "Applicant" : "Co-Applicant"}}:</v-toolbar-title>
          <v-spacer></v-spacer>
          <v-btn color="white" outline icon small @click="addIncomeDialog = false">
            <v-icon>fas fa-times</v-icon>
          </v-btn>
        </v-toolbar>
        <v-card-text>
          <v-layout row wrap>
            <v-flex xs12>
              <v-select
                :items="incomeType"
                v-model="tempIncome.type"
                label="Income Type:"
              ></v-select>
            </v-flex>
            <v-flex xs12>
              <v-select
                :items="incomeFrequency"
                v-model="tempIncome.frequency"
                label="Frequency:"
              ></v-select>
            </v-flex>
          </v-layout>
          <v-layout v-if="tempIncome.frequency">
            <v-flex xs2>
              <v-text-field
                label="Amount 1"
                v-model="tempIncome.amount1"
              ></v-text-field>
            </v-flex>
            <v-flex xs2 offset-xs1 v-if="tempIncome.frequency != 'Monthly'">
              <v-text-field
                label="Amount 2"
                v-model="tempIncome.amount2"
              ></v-text-field>
            </v-flex>
            <v-flex xs2 offset-xs1 v-if="tempIncome.frequency == 'Weekly'">
              <v-text-field
                label="Amount 3"
                v-model="tempIncome.amount3"
              ></v-text-field>
            </v-flex>
            <v-flex xs2 offset-xs1 v-if="tempIncome.frequency == 'Weekly'">
              <v-text-field
                label="Amount 4"
                v-model="tempIncome.amount4"
              ></v-text-field>
            </v-flex>
          </v-layout>
          <v-card>
            <v-card-text>
              <v-layout row wrap>
                <v-flex xs5>
                  <v-text-field
                    label="Average:"
                    prepend-inner-icon="fas fa-dollar-sign"
                    readonly
                    v-model="getTempAvg"
                  ></v-text-field>
                </v-flex>
                <v-flex xs5 offset-xs1>
                  <v-text-field
                    label="Annual:"
                    prepend-inner-icon="fas fa-dollar-sign"
                    v-model="getTempAnnual"
                    readonly
                  ></v-text-field>
                </v-flex>
              </v-layout>
            </v-card-text>
          </v-card>
        </v-card-text>
        <v-layout row wrap>
          <v-spacer></v-spacer>
          <v-btn color="primary" small @click="saveIncomeItem()" outline round>
            <v-icon left>fas fa-save</i></v-icon>
            Save Income
          </v-btn>
        </v-layout>
      </v-card>
    </v-dialog>
    <v-dialog
      v-model="deleteDialog"
      persistent :overlay="false"
      max-width="500px"
      transition="dialog-transition"
    >
      <v-card>
        <v-toolbar color="primary" class="title" dark dense>
          <v-icon>fas fa-question-circle</v-icon>
          <v-toolbar-title>Delete this income record?</v-toolbar-title>
        </v-toolbar>
        <v-card-text>
          <v-layout row wrap justify-space-around>
            <v-btn color="primary" @click="delRecord()" round outline>
              <v-icon left>fas fa-check</v-icon>
              Yes
            </v-btn>
            <v-btn color="red darken-4" @click="deleteDialog = false" dark round outline>
              <v-icon left>fas fa-times</v-icon>
              No
            </v-btn>
          </v-layout>
        </v-card-text>
      </v-card>
    </v-dialog>
  </v-card>
</template>

<script>
export default {
  props: {
    appIncome: Object,
    coAppIncome: Object
  },
  data(){
    return{
      addIncomeDialog: false,
      addIncomePerson: true,
      applicant: this.appIncome,
      coapplicant: this.coAppIncome,
      deleteDialog: false,
      tempIncome: {
        type: "",
        frequency: "",
        amount1: 0,
        amount2: 0,
        amount3: 0,
        amount4: 0
      },
      whomToDel: "",
      whomToDelIndex: null,
    }
  },
  methods: {
    addIncome(forWhom){
      this.tempIncome = {
        type: "",
        frequency: "",
        amount1: 0,
        amount2: 0,
        amount3: 0,
        amount4: 0
      }
      this.addIncomeDialog = true
      this.addIncomePerson = forWhom
    },
    confirmDelete(forWhom, index){
      this.deleteDialog = true
      this.whomToDel = forWhom
      this.whomToDelIndex = index
    },
    delRecord(){
      if (this.whomToDel == "app"){
        this.applicant.income.splice(this.whomToDelIndex, 1)
      }
      else {
        this.coapplicant.income.splice(this.whomToDelIndex, 1)
      }
      this.deleteDialog = false
    },
    getCardColor(i){
      if (i % 2 != 0) {
        return "grey lighten-5"
      }
      else {
        return "white"
      }
    },
    getTotal(){
      let grandTotal = 0
      let app = this.applicant.income
      let coapp = this.coapplicant.income
      if (app){
        app.forEach(item => {
          grandTotal = grandTotal + Number(item.total)
        })
      }
      if (coapp){
        coapp.forEach(item => {
          grandTotal = grandTotal + Number(item.total)
        })
      }
      return grandTotal.toFixed(2)
    },
    next(){
      this.$emit('next')
    },
    saveIncomeItem(){
      let incomeRecord = this.tempIncome
      incomeRecord.total = this.getTempAnnual
      if (this.addIncomePerson === true ){
        if (this.applicant.income) {
            this.applicant.income.push(incomeRecord)
        }
        else {
          this.applicant['income'] = [incomeRecord]
        }
      }
      else {
        if (this.coapplicant.income) {
            this.coapplicant.income.push(incomeRecord)
        }
        else {
          this.coapplicant['income'] = [incomeRecord]
        }
      }
      this.addIncomeDialog = false
    }
  },
  computed: {
    getTempAvg(){
      if (this.tempIncome.frequency == "Weekly"){
        return ((Number(this.tempIncome.amount1) + Number(this.tempIncome.amount2) + Number(this.tempIncome.amount3) + Number(this.tempIncome.amount4))/4).toFixed(2)
      }
      else if (this.tempIncome.frequency == "Monthly") {
        return Number(this.tempIncome.amount1).toFixed(2)
      }
      else {
        return ((Number(this.tempIncome.amount1) + Number(this.tempIncome.amount2))/2).toFixed(2)
      }
    },
    getTempAnnual(){
      let avg = this.getTempAvg
      if (this.tempIncome.frequency == "Weekly"){
        return (avg * 52).toFixed(2)
      }
      else if (this.tempIncome.frequency == "Bi-Weekly") {
        return (avg * 26).toFixed(2)
      }
      else if (this.tempIncome.frequency == "Semi-Monthly") {
        return (avg * 24).toFixed(2)
      }
      else {
        return (avg * 12).toFixed(2)
      }
    },
    incomeFrequency(){
      return this.$store.getters.getIncomeFrequency
    },
    incomeType(){
      return this.$store.getters.getIncomeType
    }
  }
}
</script>

<style lang="css" scoped>
</style>
