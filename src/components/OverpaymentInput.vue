<template>
    <div>
        <el-card class="box-card">
            <el-row :gutter="25">
                <el-col :span="10">
                    <label class="amount__label" for="amount">Amount</label>
                    <el-input-number v-model="payment" size="small" :step="100" :min="0" :max="amount" @change="update"></el-input-number>
                </el-col>
                <el-col :span="11">
                    <label class="year__label" for="amount">Year</label>
                    <el-input-number v-model="year" size="small" :step="1" :min="1" :max="duration" @change="update"></el-input-number>
                </el-col>
                <el-col :span="3">
                    <div>
                        <el-button size="small" icon="el-icon-delete-solid" v-on:click="removeOverpayment" type="danger"></el-button>
                    </div>
                </el-col>
            </el-row>
        </el-card>
    </div>
</template>

<script>
    export default {
        name: 'OverpaymentInput',
        props: ['duration', 'amount', 'index'],
        data() {
            return {
                payment: 0,
                year: 0
            }
        },
        methods: {
            removeOverpayment() {
                this.$emit("removeOverpayment", this.index);
            },
            update() {
                this.$emit("updateOverpayment", {index: this.index, payment: this.payment, year: this.year});
            }
        },
    }
</script>

<style scoped>
    .amount__label {
        display: inline-block;
        padding-right: 50px;
    }
    .year__label {
        display: inline-block;
        padding-right: 50px;
    }

    .box-card {
        margin-bottom: 10px;
    }

</style>
