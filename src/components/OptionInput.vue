<template>
    <div class="input-group">
        <label for="amount">{{this.label}}</label>
        <el-input-number v-model="val" :name="this.name" size="small" :step="this.step" :min="this.min" :max="this.max" @change="update">></el-input-number>
    </div>
</template>

<script>
    export default {
        name: 'OptionInput',
        props: ['min', 'max','step', 'name', 'label'],
        data() {
            return {
                val: 0
            }
        },
        methods: {
            update(){
                this.$emit("updateValue", {val: this.val, name: this.name});
            }
        }
    }
</script>

<style scoped>
    * {
        box-sizing: border-box;
    }
    input {
        height: 20px;
        line-height: 20px;
        padding: 2px;
        border: 1px solid black;
    }

    input:focus {
        border: 1px solid blue;
    }
    .input-group {
        display: flex;
        margin-bottom: 5px;
        text-align: left;
    }

    label {
        display: block;
        line-height: 30px;
        width: 180px;
    }
</style>
