

<script>
import { Bar } from 'vue-chartjs' 
export default {
  name: "expenditure",
  data(){
    return{
      chartData:this.expenditureData
    }
  },
  extends: Bar,
  props: ['expenditureData'],
   methods:{
    updateChartData(data){
      this.chartData=Object.assign({},data);
      this.renderChartData();
    },
    renderChartData(){
        this.renderChart(this.chartData,{responsive: true, maintainAspectRatio: false});
    }
  },
  mounted () {
    this.renderChartData();
  }
}
</script>