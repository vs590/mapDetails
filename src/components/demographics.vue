<template>
  <div>
      <div class="heading">Demographics </div>
      <div class="container">
        <div class="block" v-show="noIncomeData" style="float:left;">
            <center><h3>No Data Available</h3></center>
            <center><h4>Monthly Income</h4></center>
        </div>
        <monthly-income ref="income-chart" class="block" v-show="!noIncomeData" :incomeData="incomeData" style="float:left;"></monthly-income>
        <div class="block" v-show="noExpenditureData" style="float:right;">
            <center><h3>No Data Available</h3></center>
            <center><h4>Expenditure</h4></center>
        </div>
        <expenditure ref="expenditure-chart"  class="block" v-show="!noExpenditureData" :expenditureData="expenditureData" style="float:right;" ></expenditure>
      </div>       
 </div>
</template>

<script>
import axios from 'axios';
import monthlyIncome from '../components/monthlyIncome';
import expenditure from '../components/expenditure';
export default {
  name: 'demographics',
  data(){
      return{
          incomeData:{},
          monthlyIncomeData:{},
          expenditureData:{},
          noIncomeData:true,
          noExpenditureData:true,
          detail:this.details
      }
  },
  props:{
      details:{
          type:Object,
          required:false,
          default:undefined,
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
      },
      updateDetails(data){
          this.detail=data;
          this.populateData(this.detail);
          if(this.detail.pincode && !this.noExpenditureData)
          {
              this.$refs['expenditure-chart'].updateChartData(this.expenditureData);
          }
          if(this.detail.locality && !this.noIncomeData)
          {
               this.$refs['income-chart'].updateChartData(this.incomeData);
          }
    },
    populateData(){
       if(this.detail.pincode)
       {
          this.noIncomeData=true;
          let url="https://api.myjson.com/bins/1dpu6c";
          axios.get(url).then(response=>{
              let expenditureData=response.data;
              var chartData=expenditureData.filter(data=>{
                  return data.pincode==this.detail.pincode;
              })
              if(chartData.length>0)
              {
                  var dataset={};
                  dataset.label="Expenditure for "+chartData[0].pincode;
                  delete chartData[0].pincode;
                  this.noExpenditureData=false;
                  this.expenditureData.labels=Object.keys(chartData[0]);
                  this.expenditureData.datasets=[];
                  dataset.backgroundColor="#5eafff";
                  dataset.data=Object.values(chartData[0]);
                  this.expenditureData.datasets.push(dataset);
              }
              else
              {
                 this.noExpenditureData=true;
              }
            },
            error =>{
                alert("Error Occured While Fetching Json")
            });
        }
        else if(this.detail.locality)
        {
            this.noExpenditureData=true;
            let url="https://api.myjson.com/bins/9rnhg"
            axios.get(url).then(response=>{
                this.monthlyIncomeData=response.data;
                var chartData=this.monthlyIncomeData.filter(data=>{
                    return data.locality==this.detail.locality;
                })
                if(chartData.length>0)
                {
                    this.noIncomeData=false;
                    this.incomeData.labels=["Income for "+chartData[0].locality];
                    delete chartData[0].locality;
                    this.incomeData.datasets=[];
                    var dataset={};
                    dataset.backgroundColor="#5eafff";
                    dataset.data=Object.values(chartData[0]);
                    this.incomeData.datasets.push(dataset);
                }                 
                else
                {
                    this.noIncomeData=true;
                }
            },
            error =>{
                alert("Error Occured While Fetching Json")
            });
        }           
    }
  },
  mounted(){
      this.populateData();
  },
   components:{
      monthlyIncome,
      expenditure
  },

}
</script>
<style scoped>
.heading{
    font-size: 40px;
    color: dodgerblue;
    text-decoration: underline;
    margin-bottom: 30px;
}
.container{
    display: inline-flex;
    width: 100%;
}
.block{
    width: 50%;
    color: dodgerblue;
}
</style>
