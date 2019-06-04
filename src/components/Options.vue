<template>
    <div>
        <div class="options-container">
            <el-card class="box-card">
                <div slot="header" class="clearfix">
                    <span>Options</span>
                </div>

                <OptionInput name="amount" label="Loan Amount (£)" :step=5000 :min=40000 :max=400000 @updateValue="updateVal"/> 
                <OptionInput name="duration" label="Loan Duration (Years)" :step=1 :min=1 :max=35 @updateValue="updateVal"/> 
                <OptionInput name="interest" label="Interest (APR)" :step=0.005 :min=0.01 :max=0.10 @updateValue="updateVal"/> 

                <el-button v-on:click="displayTable = true" type="primary">Calculate!</el-button>
            </el-card>
        </div>

        <div class="overpayment-container">
            <el-card class="box-card">
                <div slot="header" class="clearfix">
                    <span>Overpayments</span>
                </div>
                <div v-for="(overpayment) in overpayments" :key="overpayment.index">
                    <OverpaymentInput :duration="duration" :index="overpayment.index" :min=0 :max="amount" @updateOverpayment="updateOverpayment" @removeOverpayment="removeOverpayment"/>
                </div>   
                <el-button v-on:click="addOverpayments" type="primary">Add an overpayment</el-button>
            </el-card>
        </div>
        <div style="text-align: center">
            <h3 v-if="displayTable">Monthly payment: {{calculatePayments | formatMoney}}</h3>
        </div>
        <div class="container-center">
            <el-table
                v-if="displayTable"
                :data="years"
                border
                style="width: 100%">
                <el-table-column
                prop="year"
                label="Year"
                width="150">
                </el-table-column>
                <el-table-column
                prop="remaining"
                :formatter="remainingFormatter" 
                label="Remaining"
                width="180">
                </el-table-column>
                <el-table-column
                prop="interestPaid"
                :formatter="interestPaidFormatter"
                label="Interest Paid">
                </el-table-column>
                <el-table-column
                prop="principlePaid"
                :formatter="principlePaidFormatter"
                label="Balance Paid">
                </el-table-column>
                <el-table-column
                prop="totalPaid"
                :formatter="totalPaidFormatter"
                label="Total Paid">
                </el-table-column>
                <el-table-column
                prop="overpayment"
                :formatter="overpaymentFormatter"
                label="Overpayment">
                </el-table-column>
            </el-table>
        </div>
        <div v-if="displayTable" class="container-center">
            <el-card class="box-card">
                <Chart :years="years" :options="options" />
                <bar :years="years" />
            </el-card>
        </div>
    </div>
</template>

<script>
    import OptionInput from './OptionInput.vue'
    import OverpaymentInput from './OverpaymentInput.vue'
    import Chart from './Chart.vue'
    import Bar from './Bar.vue'

    export default {
        data() {
            return {
                amount: 40000,
                duration: 1,
                interest: 0.01,
                displayTable: false,
                overpayments: [],
                overpaymentCount: 0,
                options: {     
                    responsive: true,
                    maintainAspectRatio: false
                }
            }
        },
        name: 'Options',
        components: {
            OptionInput,
            OverpaymentInput,
            Chart,
            Bar
        },
        computed: {
            years() {
                let yearArray = [];
                let remaining = this.amount;
                let interestPaid, principlePaid;
                let totalPaid = 0;

                yearArray.push({
                    year: 0,
                    remaining,
                    interestPaid: 0,
                    principlePaid: 0,
                    totalPaid: 0,
                    overpayment: 0 
                })

                for (let i = 0; i < this.duration; i++){
                    const overpayment = this.overpayments.find(function(overpayment) {
                        return overpayment.year === i + 1;
                    });
                    if (overpayment) {
                        remaining -= overpayment.payment;
                    }
                    interestPaid = 0;
                    principlePaid = 0;
                    for (let j = 0; j < 12; j++){
                        if (remaining <= 0) {
                            break;
                        }
                        interestPaid += remaining * (this.interest/12);
                        principlePaid += this.calculatePayments - (remaining * (this.interest/12));
                        totalPaid += this.calculatePayments;   
                        remaining -= this.calculatePayments - (remaining * (this.interest/12));
                        if (remaining < 0) {
                            let difference = 0 - remaining;
                            totalPaid -= difference;
                            remaining = 0;
                        }
                    }
                    const payment = overpayment ? overpayment.payment : 0;

                    let year = {
                        year: i + 1,
                        remaining,
                        interestPaid,
                        principlePaid,
                        totalPaid,
                        overpayment: payment
                    };
                    yearArray.push(year);
                }
                return yearArray
            },
            calculatePayments() {
                let top = (this.interest/12)*Math.pow(1 + this.interest/12, 12*this.duration);
                let bottom = (Math.pow(1 + this.interest/12,12*this.duration)) - 1;
                return this.amount*(top/bottom);
            }
        },
        filters: {
            formatMoney: function (value) {
                if (!value) return ''
                value = value.toFixed(2);
                return `£${value.toString()}`
            }
        },
        methods: {
            remainingFormatter(row) {
                let value = row.remaining.toFixed(2);
                return `£${value.toString()}`
            },
            interestPaidFormatter(row) {
                let value = row.interestPaid.toFixed(2);
                return `£${value.toString()}`
            },
            principlePaidFormatter(row) {
                let value = row.principlePaid.toFixed(2);
                return `£${value.toString()}`
            }, 
            totalPaidFormatter(row) {
                let value = row.totalPaid.toFixed(2);
                return `£${value.toString()}`
            },
            overpaymentFormatter(row) {
                let value = row.overpayment.toFixed(2);
                return `£${value.toString()}`
            },
            addOverpayments() {
                this.overpaymentCount ++;
                this.overpayments.push({
                    year: null,
                    payment: 0,
                    index: this.overpaymentCount
                })
            },
            updateVal(payload) {
                this[payload.name] = payload.val;
            },
            removeOverpayment(index) {
                this.overpayments = this.overpayments.filter( (item) => item.index !== index);
            },
            updateOverpayment(data) {
                this.overpayments = this.overpayments.map((payment) => {
                    if (payment.index === data.index){
                        return data;
                    }
                    return payment;
                })
            }  
        }

    }
</script>

<style scoped>
    * {
        box-sizing: border-box;
    }

    .input-wrapper {
        display: inline-block;
        margin-bottom: 20px;
    }
    .options-container {
        width: 800px;
        margin: 20px auto 40px;
    }

    .el-table {
        max-width: 1000px;
        margin: 20px auto;
    }
    td {
        border: 1px solid black;
        padding: 2px;
    }

    .container-center {
        width: 800px;
        margin: 20px auto;
    }

    .overpayment-container {
        width: 800px;
        margin: 20px auto;        
    }
</style>