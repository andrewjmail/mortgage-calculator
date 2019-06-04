<script>
import { Bar } from 'vue-chartjs'

export default {
    extends: Bar,
    props: ['years'],
    mounted () {
        this.getChartData(this.years);
    },
    data() {
        return {
            chartdata: {},
            barOptions: {
                scales: {
                    xAxes: [{ stacked: true, label: "Years" }],
                    yAxes: [{ stacked: true }]
                }
            }
        }
    },
    watch: {
        years: function(newVal) {
            this.getChartData(newVal);
        }
    },
    methods: {
        getChartData(years) {
            years = years.slice(1);
            const princplePaid = years.map(year => {
                return year.principlePaid.toFixed(2);
            });
            const interestPaid = years.map(year => {
                return year.interestPaid.toFixed(2);
            });            
            const labels = years.map(year => {
                return year.year;
            });

            this.chartdata =  {
                labels,
                datasets: [{
                    label: 'Principle',
                    data: princplePaid,
                    backgroundColor: '#D6E9C6' // green
                },
                {
                    label: 'Interest',
                    data: interestPaid,
                    backgroundColor: '#FAEBCC' // yellow
                }
                ]               
            }   
            this.renderChart(this.chartdata, this.barOptions)
        }
    },
}
</script>