<template>
<div class="main-conatiner">
  <div class="details-container">
    <div class="detail-data-container" v-if="isLocationSelected">
      <div style="float:left;" class="detail-data-subcontainer1">
        <span class="data-left">{{selectedData.population}}</span>
        <span class="data-right">{{selectedData.households}}</span>
      </div>
      <div style="float:right;" class="detail-data-subcontainer2">
        <span class="sub-data-left">Population</span>
        <span class="sub-data-right">Households</span>
      </div>
      <div class="address-details">
        <span class="identifier-detail">{{getLocalityOrDistrictName}}</span>
        <span class="country-detail">City: {{selectedData.city?selectedData.city:selectedData.district_n}}</span>
      </div>
    </div>
    <div class="container-text" v-if="!isLocationSelected">
      Please Select the Location Right side of the Map (*Selected Area)
    </div>
  </div>
  <div id='map'></div>
</div>
</template>

<script>
export default {
  name: 'ShowMap',
  data () {
    return {
      selectedData:{},
      popup:null,
      jsonDataUrl:this.jsonDataURL
    }
  },
  computed:{
    isLocationSelected(){
      return Object.keys(this.selectedData).length!=0
    },
    getLocalityOrDistrictName()
    {
      return this.isLocality?"Locality: "+this.selectedData.locality:"District: "+this.selectedData.district_n
    }
  },
  props:{
    accessToken:{
      type:String,
      required:true
    },
    jsonDataURL:{
      type:String,
      required:true
    },
    isLocality:{
      type:Boolean,
      required:false,
      default:true
    }
  },
  methods:{
    createPolygon(){
      this.map.addLayer({
        'id': 'states-layer',
        'type': 'fill',
        'source': {'type': 'geojson','data': this.jsonDataUrl},
        'paint': {'fill-color': 'rgba(25, 181, 254, 0.4)','fill-outline-color': 'rgba(31, 58, 147, 1)'}
      });
    },
    changePolygon(dataURL){
      this.jsonDataUrl=dataURL;
      this.selectedData={};
      if(this.popup!=null)
        this.popup.remove();
      this.map.removeLayer('states-layer');
      this.map.removeSource('states-layer');
      this.createPolygon();
    },
    showPopup(locationDetail){
      this.selectedData=locationDetail.features[0].properties;
      this.$emit("selectedDetails",this.selectedData);
      var popupContent=this.preparePopupHTML(this.selectedData);
      this.popup=new mapboxgl.Popup().setLngLat(locationDetail.lngLat).setHTML(popupContent).addTo(this.map);
    },
    preparePopupHTML(locationDetail)
    {
      var col1Heading=this.isLocality?"Locality":"Pincode";
      var col1SubText=this.isLocality?locationDetail.locality:locationDetail.pincode;
      return "<div style='padding:5px;'><table border=1><tr><th>"+col1Heading+"</th><th>Population</th><th>Household</th></tr><tr><td>"+col1SubText+"</td><td>"+locationDetail.population+"</td><td>"+locationDetail.households+"</td></tr></table><center><input id='show-data' style='background:black; color:white; border-radius:20px; height:30px; margin-top:5px;' type='button' value='View Data' @click='showData()'/></center></div>"
    },
    showData(){
      let data=this.selectedData;
    },
    initializeMap(){
      mapboxgl.accessToken = this.accessToken;
      this.map = new mapboxgl.Map({
      container: 'map',
      style: 'mapbox://styles/mapbox/streets-v11',
      center: [77.580643, 12.972442],
      zoom: 11
      });
    }
  },
  mounted(){
    this.initializeMap();
    this.map.on("load",()=>{
      this.createPolygon();
    })
    this.map.on('click', 'states-layer', locationDetail=> {
       this.showPopup(locationDetail);
    });
    this.map.on('mouseenter', 'states-layer', function (e) {
      this.getCanvas().style.cursor = 'pointer';
    });
    this.map.on('mouseleave', 'states-layer', function (e) {
      this.getCanvas().style.cursor = '';
    });
  },  
}
</script>

<style scoped>
#map {
  display:inline;
  width: 50%;
  height: 500px;
  float: right;
}
.address-details{
    display: inline-block;
    width: 100%;
    height: 45px;
    background: white;
    margin-top: 20px;
}
.identifier-detail{
    float: left;
    color: cornflowerblue;
    font-size: 20px;
    margin-top: 10px;
    margin-left: 120px;
    display: inline;
}
.country-detail{
    float: right;
    color: cornflowerblue;
    font-size: 20px;
    margin-top: 10px;
    margin-right: 176px;
    display: inline
}
.detail-data-container{
    margin-top: 20px;
    width:100%;
}
.detail-data-subcontainer1{
    display: inline-block;
    position: absolute;
    width: 50%;
}
.detail-data-subcontainer2{
    margin-top: 46px;
    display: inline-block;
    width: 100%;
}
.data-left{
    margin-left: 200px;
    font-size: 30px;
    color: dodgerblue;
    font-weight: 900;
    display: inline;
}
.data-right{
    font-size: 30px;
    color: dodgerblue;
    float: right;
    font-weight: 900;
    margin-right: 205px;
    display: inline;
}
.sub-data-left{
    margin-left: 190px;
    font-size: 30px;
    float: left;
    display: inline;
}
.sub-data-right{
    margin-right: 170px;
    font-size: 30px;
    float: right;
    display: inline;
}
.mapboxgl-popup {
  max-width: 310px !important;
  font: 12px/20px 'Helvetica Neue', Arial, Helvetica, sans-serif;
}

.container-text{
  font-size: 30px;
  text-align: center;
  margin-top: 250px;
}
.details-container{
  display:inline;
  background: aliceblue;
  width: 50%;
  height: 500px;
  float: left;
}
.main-conatiner{
  display: inline-block;
  width: 100%;
}
</style>
