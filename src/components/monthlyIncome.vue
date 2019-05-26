
<script>
import { Doughnut } from 'vue-chartjs' 
export default {
  name: "monthlyincome",
  data(){
    return{
      chartData:this.incomeData
    }
  },
  extends: Doughnut,
  props: ['incomeData'],
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
  },
  
}
</script>