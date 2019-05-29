<script>
import { Pie } from 'vue-chartjs'

export default {
    extends: Pie,
    props: ['years', 'options'],
    mounted () {
        this.getChartData(this.years);
    },
    data() {
        return {
            chartdata: {}
        }
    },
    watch: {
        years: function(newVal) {
            this.getChartData(newVal);
        }
    },
    methods: {
        getChartData(years) {
            const interest = years.reduce( (acum, year) => {
                return acum + year.interestPaid;
            }, 0).toFixed(2);

            const principle = years.reduce( (acum, year) => {
                return acum + year.principlePaid;
            }, 0).toFixed(2);

            this.chartdata =  {
                labels:  ['interest', 'principle'],
                datasets: [{
                    backgroundColor: ['#f87979', '#aceace'],
                    data: [interest, principle]
                }]
            }     
            this.renderChart(this.chartdata, this.options)
        }
    }
}
</script>
