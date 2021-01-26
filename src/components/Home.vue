<template>
  <div class="main-page">
    <div class="form row">
      <div class="col-12">
        <h2 id="title" class="title" style="font-size: xx-large; color: darkcyan;text-align: center;">SOLAR ENERGY</h2>
      </div>
      <div class="col-12">
        <label class="time">
          <span class="time-span">Time(month): </span>
          <select class="chooser-select">
            <option class="chooser-option" value="January">January</option>
            <option class="chooser-option" value="February">February</option>
            <option class="chooser-option" value="March">March</option>
            <option class="chooser-option" value="April">April</option>
            <option class="chooser-option" value="May">May</option>
            <option class="chooser-option" value="June">June</option>
            <option class="chooser-option" value="July">July</option>
            <option class="chooser-option" value="August">August</option>
            <option class="chooser-option" value="September">September</option>
            <option class="chooser-option" value="October">October</option>
            <option class="chooser-option" value="November">November</option>
            <option class="chooser-option" value="December">December</option>
          </select>
        </label>
      </div>

      <div class="col-12">
        <label class="input-ray pt-4">
          <span class="input-ray-span">Input rays(by Poisson):</span>
          <input id="input-ray" type="range" min="0" max="2000" v-model="input_ray" step="1"  value="500">
          <output>500</output>
        </label>
      </div>

      <div class="col-12">
        <label class="S1 pt-4">
          <span class="S1-span">S1(Solar panel): </span>
          {{input_ray }} * 20.1% = {{resultS1}}
        </label>
      </div>

      <div class="col-12 pt-4">
        <label class="P1">
          <span class="P1-span">P1(DC cable diameter): </span>
          <select id="chooser-select-p1" class="chooser-select" v-model="onChangeP1">
            <option class="chooser-option" value="2.5">2.5 mm</option>
            <option class="chooser-option" value="6">6 mm</option>
            <option class="chooser-option" value="4">4 mm</option>
          </select>
        </label>
      </div>

      <div class="col-12">
        <label class="C pt-4">
          C(efficiency loss):
          <input type="text" v-model="myTextC" readonly>%
        </label>
      </div>

      <div class="col-12">
        <label class="P2 pt-4">
          <span class="P2-span" >P2(AC cable diameter): </span>
          <select id="chooser-select-p2" class="chooser-select" type="number" v-model="onChangeP2">
            <option class="chooser-option" value="400">400 mm2</option>
            <option class="chooser-option" value="300">300 mm2</option>
            <option class="chooser-option" value="240">240 mm2</option>
          </select>
        </label>
      </div>

      <div class="col-12">
        <label class="C pt-4">
          S2 (efficiency loss): <input type="number" id="myTextS2" v-model="myTextS2" readonly>%
        </label>
      </div>

      <div class="col-12">
        <label class="P3 pt-4">
          <span class="P3-span">P3(AC cable diameter): </span>
          <select id="chooser-select-p3" type="number"  class="chooser-select" v-model="onChangeP3">
            <option class="chooser-option" value="16">16 mm</option>
            <option class="chooser-option" value="25">25 mm</option>
            <option class="chooser-option" value="35">35 mm</option>
          </select>
        </label>
      </div>
      <div class="col-12">
        <label class="C pt-4">
          S3 (efficiency loss): <input type="text" v-model="myTextS3" readonly>%
        </label>
      </div>

      <div class="col-12">

        <label class="C pt-4">
          <div id="input-C">C(Solar inverter): {{calculateC()}}</div>
        </label>
      </div>
      <div class="col-12">
        <label class="S2 pt-4">
          <div id="input-S2">S2(Grid): {{calculateS2()}}</div>
        </label>
      </div>
      <div class="col-12">
        <label class="S3 pt-4">
          <div id="input-S3">S3(Solar accumulator): {{calculateS3()}}</div>
        </label>
      </div>
    </div>
    <div class="container">
      <div class="row">
        <div class="col-12">
          <vue-funnel-graph :width="800" :height="300"
                            :labels="labels()"
                            :values="values()"
                            :colors="['#eaa62a', '#7795FF', '#FF3C8E']"
                            :sub-labels="subLabels()"
                            :direction="direction"
                            :gradient-direction="gradientDirection"
                            :animated="true"
                            :display-percentage="true"
          ></vue-funnel-graph>
        </div>
      </div>
    </div>

  </div>
</template>

<script>
import { VueFunnelGraph } from 'vue-funnel-graph-js';

export default {
  name: 'Home',
  components: {
    VueFunnelGraph,
  },

  data() {
    return {
      resultS1: 0,
      input_ray: 0,
      sunny_rate: 0,
      onChangeP1: 2.5,
      myTextC: 1.7,
      onChangeP2: 400,
      myTextS2: 6.0,
      onChangeP3: 16,
      myTextS3: 16,
      tempResultC: 0.0,
      tempResultS2: 0.0,
      tempResultS3: 0.0,
      inputC: 0.0,
      inputS2: 0.0,
      inputS3: 0.0,
      direction: 'horizontal',
      gradientDirection: 'horizontal',

    };
  },

  mounted() {
    this.input_ray = 500;
  },


  methods:{
    labels(){
      let labels = ['S1', 'S2', 'S3']
      return labels
    },
    values(){
      return [Math.round(this.tempResultC), Math.round(this.tempResultS2), Math.round(this.tempResultS3)];
    },
    subLabels(){
      return [Math.round(this.tempResultC), Math.round(this.tempResultS2), Math.round(this.tempResultS3)];
    },

    calculateC() {
      let value = this.onChangeP1;
      let inputC  = ""

      if(value === "2.5") {
        this.tempResultC = (parseFloat(this.resultS1)* 88.5 * (100 - 1.7)).toFixed(3);
        inputC = `C(Solar inverter): ${this.resultS1} * 88.5% * (100% - 1.7%) = ${this.tempResultC}`;
      } else if (value === "6") {
        this.tempResultC = (parseFloat(this.resultS1) * 88.5 * (100 - 0.8)).toFixed(3);
        inputC = `C(Solar inverter): ${this.resultS1} * 88.5% * (100% - 0.8%)= ${this.tempResultC}`;
      } else {
        this.tempResultC = (parseFloat(this.resultS1) * 88.5).toFixed(3);
        inputC = `C(Solar inverter): ${this.resultS1} * 88.5% = ${this.tempResultC}`;
      }

      this.inputC = inputC;
      return inputC;
    },

    calculateS2() {
      let value = this.onChangeP2;
      let inputS2 = "";

      if (value === "400") {
        this.tempResultS2 = (this.tempResultC * 0.8 *(100 - 6.0)).toFixed(3);
        inputS2 = `S2(Grid): ${this.tempResultC} * 0.8 * (100% - 6%) = ${this.tempResultS2}`;
      } else if (value === "300") {
        this.tempResultS2 = (this.tempResultC * 0.8 * (100 - 6.7)).toFixed(3);
        inputS2 = `S2(Grid): ${this.tempResultC} * 0.8 *(100% - 6.7%)= ${this.tempResultS2}`;
      } else {
        this.tempResultS2 = (this.tempResultC * 0.8 * (100 - 7.0)).toFixed(3);
        inputS2 = `S2(Grid): ${this.tempResultC} * 0.8 *(100% - 7%)= ${this.tempResultS2}`;
      }

      this.inputS2 = inputS2;
      return inputS2;
    },

    calculateS3(){
      let value = this.onChangeP3;
      let inputS3 = "";

      if(value === "16") {
        this.tempResultS3 = (this.tempResultC * 0.2 * (100 - 16)).toFixed(3);
        inputS3 = `S3(Solar accumulator): ${this.tempResultC} * 0.2 * (100% - 16%) = ${this.tempResultS3}`
      } else if (value === "25") {
        this.tempResultS3 = (this.tempResultC * 0.2 * (100 - 18)).toFixed(3);
        inputS3 = `S3(Solar accumulator): ${this.tempResultC} * 0.2 * (100% - 18%) = ${this.tempResultS3}`
      } else {
        this.tempResultS3 = (this.tempResultC * 0.2 * (100 - 15)).toFixed(3);
        inputS3 = `S3(Solar accumulator): ${this.tempResultC} * 0.2 * (100% - 15%) = ${this.tempResultS3}`
      }

      this.inputS3 = inputS3;
      return inputS3;
    }
  },

  watch: {
    onChangeP1(){
      let value = this.onChangeP1;
      if (value === "2.5") {
        this.myTextC = "1.7";
      } else if (value === "6") {
        this.myTextC = "0.8";
      } else {
        this.myTextC = "88.5";
      }
    },

    onChangeP2(){
      let value = this.onChangeP2;
      if (value === "400") {
        this.myTextS2 = "6.0";
      } else if (value === "300") {
        this.myTextS2 = "6.7";
      } else {
        this.myTextS2 = "7.0";
      }
    },

    onChangeP3(){
      let value = this.onChangeP3;
      if(value === "16"){
        this.myTextS3 = "16.0";
      } else if (value === "25"){
        this.myTextS3 = "18.0";
      } else {
        this.myTextS3 = "15.0";
      }

    },

    input_ray(){
      this.resultS1 = this.input_ray * 20.1 / 100
    }
  },
}
</script>

<style scoped>
.funnel {
  margin: 24px auto;
}

.example-funnels {
  display: flex;
  flex-direction: column;
}
html, body {
  min-height: 100%;
}

body{
  background: linear-gradient(45deg, rgb(0, 167, 204), rgb(255, 138, 128));
  background-size: 100% auto;
  background-position: center center;
  margin: 0;
  width: 100%;
  height: 100%;
}

.form {
  position: relative;
  z-index: 1;
  background: lightcyan;
  max-width: 800px;
  margin: 0 auto 100px;
  margin-top: 123px;
  padding: 45px;

  box-shadow: 0 0 20px 0 rgba(0, 0, 0, 0.2), 0 5px 5px 0 rgba(0, 0, 0, 0.24);
}

.funnels {
  height: 560px;
  margin-top: 16px;
}

.funnel:not(.svg-funnel-js--vertical) {
  transition: transform 0.3s ease;
  transform: translateY(60px);
}

button {
  background-color: #21FFA2;
  color: #393862;
  border-radius: 4px;
  border: none;
  padding: 12px 24px;
  margin: 0 5px;
  font-size: 16px;
  outline: 0;
  cursor: pointer;
}

button:hover {
  background: #05DF9D;
}

@media screen and (max-width: 960px) {
  display: none;
}

.repo {
  background-color: #21FFA2;
  color: #393862;
  font-family: "Open Sans", sans-serif;
  border-radius: 10px 0 0 10px;
  display: flex;
  padding: 8px 12px;
  align-items: center;
  margin: 8px 0;
  text-decoration: none;
  transform: translateX(20px);
  transition: transform 0.3s ease;
}

&:hover {
   background: #05DF9D;
   transform: translateX(0);
}

i {
  font-size: 36px;
  margin-right: 12px;
}


</style>
