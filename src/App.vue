<template>
  <div id="app">
    <nav-bar :isLocality="isLocality" :details="details" @isPincodeView="switchData"></nav-bar>
    <show-map ref="showMap" :isLocality="isLocality" :accessToken="accessToken" :jsonDataURL="jsonDataURL" @selectedDetails="setSelectedDetails"></show-map>
    <demographics ref="updateDemographics" v-show="isLocationSelected"  :details="details"></demographics>
  </div>
</template>

<script>
import demographics from './components/demographics'
import navBar from './components/navBar';
import showMap from './components/showMap';
export default {
  name: 'App',
  data(){
    return {
      map:{},
      isLocality:true,
      details:{},
      jsonDataURL:"https://api.myjson.com/bins/qz2v8",
      accessToken:'pk.eyJ1IjoidnM1OTAiLCJhIjoiY2p3MzcxZDN1MGE3bTQzczB5N2pzaDlwYSJ9.BXpeQ1he682PtiKuzd0CKg'
    };
  },
  computed:{
    isLocationSelected(){
        return Object.keys(this.details).length!=0
    },
  },
  methods: {
   switchData(isPincodeView){
     this.jsonDataURL=isPincodeView ?"https://api.myjson.com/bins/g0guk":"https://api.myjson.com/bins/qz2v8";
     this.isLocality=!isPincodeView;
     this.$refs['showMap'].changePolygon(this.jsonDataURL);
     this.details={};
   },
   setSelectedDetails(detail){
     this.details=detail;
     this.$refs.updateDemographics.updateDetails(this.details);
   }
  },
  components: {
    showMap,
    navBar,
    demographics
  },
}
</script>

