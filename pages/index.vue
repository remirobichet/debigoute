<template>
  <div>
    <b-container class="mb-5">
      <b-row class="my-3">
        <b-col sm="3">
          <label for="base-liquid">Poche de base (mL)</label>
        </b-col>
        <b-col sm="9">
          <b-form-input
            v-model.number="baseLiquid"
            id="base-liquid"
            type="number"
          ></b-form-input>
        </b-col>
      </b-row>
      <hr />
      <b-row class="my-3" v-for="(el, index) in addedLiquid" :key="el.index">
        <b-col sm="3">
          <label :for="'base-liquid-' + index"
            >Solution additionnelle n° {{ index + 1}} (mL)</label
          >
        </b-col>
        <b-col cols="6" sm="6">
          <b-form-input
            v-model.number="el.value"
            :id="'base-liquid-' + index"
            type="number"
          ></b-form-input>
        </b-col>
        <b-col cols="6" sm="3" v-if="addedLiquid.length > 1">
          <b-button variant="outline-primary" @click="deleteRow(index)"
            >Supprimer</b-button
          >
        </b-col>
      </b-row>
      <b-row class="my-3">
        <b-col sm="12">
          <b-button variant="outline-primary" @click="addRow"
            >Ajouter une ligne</b-button
          >
        </b-col>
      </b-row>
      <hr />
      <b-row class="my-3">
        <b-col sm="3">
          <label for="time">Temps (heure)</label>
        </b-col>
        <b-col sm="9">
          <b-form-input
            v-model.number="time"
            id="time"
            type="number"
          ></b-form-input>
        </b-col>
      </b-row>
      <hr />
      <b-row class="my-3">
        <b-col sm="3">
          <label for="drop">Equivalence 1ml = ? gouttes</label>
        </b-col>
        <b-col sm="9">
          <b-form-input
            v-model.number="drop"
            id="drop"
            type="number"
          ></b-form-input>
        </b-col>
      </b-row>
    </b-container>
    <b-container v-if="result || result.res">
      <b-card title="Résultats" class="my-3">
        <b-card-text>{{ result.res }} gouttes/minutes</b-card-text>
      </b-card>
      <b-card title="Calculs" class="my-3">
        <b-card-text>
          <p>
            Total des Solution additionnelle :
            <span v-for="(el, index) in result.addedLiquid" :key="el.index">
              {{ el.value }}
              <template v-if="index !== result.addedLiquid.length - 1">
                +
              </template></span
            >
            =
            {{ result.totalAddedLiquid }}mL
          </p>
          <p>
            Total des Solution (Solution de base + Total solutions
            additionnelle):
            {{ result.totalAddedLiquid }}
            +
            {{ result.baseLiquid }}
            =
            {{ result.totalLiquid }}mL
          </p>
          <p>
            Total des gouttes : <br />
            {{ result.drop }}mL = 1 goutte <br />
            {{ result.totalLiquid }}mL = {{ result.totalDrop }} gouttes
          </p>

          <p>
            Exprimé en gouttes/minutes : <br />
            {{ result.totalDrop }} gouttes en {{ result.time }} heures <br />
            {{ result.totalDrop }} gouttes en {{ result.timeMinute }} minutes
            <br />
            {{ result.res }} gouttes en 1 minute
          </p>
        </b-card-text>
      </b-card>
    </b-container>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'

let baseLiquid: number = 0
let addedLiquid: any[] = [{ value: 0 }]
let time: number = 0
let drop: number = 0

let totalLiquid: number = 0
let totalAddedLiquid: number = 0
let totalDrop: number = 0

export default Vue.extend({
  data() {
    return {
      baseLiquid: baseLiquid,
      addedLiquid: addedLiquid,
      time: time,
      drop: drop,
      totalLiquid: totalLiquid,
      totalAddedLiquid: totalAddedLiquid,
      totalDrop: totalDrop,
    }
  },
  methods: {
    addRow() {
      this.addedLiquid.push({
        value: 0,
      })
    },
    deleteRow(index: number) {
      this.addedLiquid.splice(index, 1)
    },
  },
  computed: {
    result(): Object {
      this.totalAddedLiquid = 0
      this.totalLiquid = 0

      this.addedLiquid.forEach((el) => {
        this.totalAddedLiquid += el.value
      })

      this.totalLiquid = this.totalAddedLiquid + this.baseLiquid

      if (
        !this.time ||
        this.time === 0 ||
        !this.totalLiquid ||
        this.totalLiquid === 0 ||
        !this.drop ||
        this.drop === 0
      ) {
        return false
      }

      this.totalDrop = this.totalLiquid * this.drop

      let timeMinute
      timeMinute = this.time * 60

      let res = this.totalDrop / timeMinute

      return {
        baseLiquid: this.baseLiquid,
        addedLiquid: this.addedLiquid,
        totalAddedLiquid: this.totalAddedLiquid,
        totalLiquid: this.totalLiquid,
        drop: this.drop,
        totalDrop: this.totalDrop,
        time: this.time,
        timeMinute: timeMinute,
        res: res,
      }
    },
  },
})
</script>

<style></style>
