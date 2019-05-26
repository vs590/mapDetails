<template>
  <div class="nav-bar">
      <div class="heading"><img class="logo" src="https://img.icons8.com/ultraviolet/50/000000/file.png" alt="Report"><h3 style="display:inline;"> Report</h3></div>
    <span style="float:right;"><input class="action-button" type="button" :value="getButtonText" @click="switchTab()"/></span>
    <div class="sub-heading"><h4 v-show="isLocationSelected">Individual Report for {{getSubText}}</h4></div>
  </div>
</template>

<script>
export default {
  name: 'ShowMap',
  computed : {
      isLocationSelected(){
        return Object.keys(this.details).length!=0
      },
      getButtonText(){
          return this.isLocality?"Pincode":"Locality";
      },
      getSubText(){
          return this.details.pincode?this.details.pincode:"Locality ID: "+this.details.locality_i;
      }
  },
  methods:{
      switchTab(){
          if(this.isLocality)
          {
              this.$emit("isPincodeView",true);
          }
          else{
              this.$emit("isPincodeView",false);
          }
      }
  },
  props:{
      isLocality:{
          type:Boolean,
          required:true,
      },
      details:{
          type:Object,
          required:false,
          default:undefined
      }
      
  }

}
</script>

<style scoped>
.action-button{
    background: black;
    color: white;
    width: 100px;
    height: 40px;
    margin: 30px;
    font-size: 18px;
    cursor: pointer;
    text-transform: uppercase;
}
.sub-heading {
    margin-left: 70px;
    color: white;
}
.nav-bar{
    display: block;
    width: 100%;
    top: 0;
    height: 130px;
    background-color: dodgerblue;
}
.heading{
    font-size: 31px;
    color: white;
    display: inline-block;
    margin-left: 70px;
}
.logo {
    display: inline;
    height: 36px;
    margin-top: 20px;
}

</style>
