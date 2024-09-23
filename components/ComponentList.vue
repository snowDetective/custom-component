<template>
    <div>
        <div v-if="component.typeId" v-for="(component, index) in components" :key="index" class="component">
            <div class="labelTitle"><span class="req" v-if="component.required == '1'">*</span>{{
                component.title }}:</div>
            <component :is="component.type" :data.sync="component" style="width: 90%" :parentData="assembly[index]">
            </component>
            <el-button class="labelT" @click="removeComponent(index)" type="" v-if="!button">删除组件</el-button>
        </div>
        <div v-if="component.data" v-for="(component, index) in components" :key="index" class="component">
            <div class="labelTitle"><span class="req" v-if="component.data.required == '1'">*</span>{{
                components[index].data.title }}:</div>
            <component :is="component.type" :data.sync="component.data" style="width: 90%"
                :parentData="assembly[index]">
            </component>
            <el-button class="labelT" @click="removeComponent(index)" type="">删除组件</el-button>
        </div>
    </div>
</template>

<script>
// import { createProject, uploadOSS, regionSelect, schoolSelect, getProjectTypeSelect } from '@/apiUtils'
import { notEmpty } from 'assist-tools'
import { fromPairs } from 'lodash-es'
import ComponentAdd from '../components/ComponentAdd.vue'
import inputBox from '../components/CustomComponents/inputBox.vue'
import textField from '../components/CustomComponents/textField.vue'
import dateYearMonth from '../components/CustomComponents/dateYearMonth.vue'
import dateYearMonthDay from '../components/CustomComponents/dateYearMonthDay.vue'
import dateHourMinuteSecond from '../components/CustomComponents/dateHourMinuteSecond.vue'
import pictures from '../components/CustomComponents/pictures.vue'
import movie from '../components/CustomComponents/movie.vue'
// 级联与普通选择器组件
import choose from '../components/CustomComponents/choose.vue'
import selectMultiple from '../components/CustomComponents/selectMultiple.vue'
import cascader from '../components/CustomComponents/cascader.vue'
import cascaderMultiple from '../components/CustomComponents/cascaderMultiple.vue'
export default {
    components: {
        ComponentAdd,
        inputBox,
        movie,
        dateYearMonth,
        dateYearMonthDay,
        dateHourMinuteSecond,
        pictures,
        textField,
        choose,
        selectMultiple,
        cascader,
        cascaderMultiple
    },
    props: {
        // 传入组件内容
        assembly: {
            required: true
        },
        components: {
            required: true
        },
        dataCom: {
        },
        // 是否隐藏按钮
        button: {
            type: Boolean,
            default: false
        }
    },
    data() {
        return {
            // 自定义表单
            component: false,
            // 导入的级联内容
            cascader: [],
            // 导入的选择器内容
            selectList: [],
            action: '',
            children: ''
        }
    },
    methods: {
        removeComponent(index) {
            this.components.splice(index, 1)
            this.assembly.splice(index, 1)
            this.dataCom.splice(index, 1)
            this.$emit('comDelete', this.dataCom, this.components, this.assembly)
        }
    }
}
</script>

<style scoped lang="scss">
@import '~@/style/global.scss';

.btn-container {
    margin-top: 20px;
    @include flex(flex-end);
}

.tem {
    margin-bottom: 10px;
}

.inputTime {
    width: 31%;
    margin-right: 2.3%;
}

.component {
    display: flex;
    width: 100%;
    justify-content: center;
    align-items: center;
    margin-top: 10px;
    margin-bottom: 10px;
}

.pd10 {
    display: flex;
    width: 100%;
    display: flex;
    flex-direction: row;
}

.add {
    display: flex;
    width: 40px;
    justify-content: center;
    align-items: center;
    border: 1px solid rgb(216, 212, 212);
    color: gray;
    border-radius: 6px;
}

.add:hover {
    background-color: rgb(228, 228, 228);
}

.upAdd {
    display: flex;
    float: right;
    width: 35px;
    justify-content: center;
    align-items: center;
    border: 1px solid rgb(216, 212, 212);
    color: gray;
    border-radius: 6px;
}

.upAdd:hover {
    background-color: rgb(228, 228, 228);
}

.labelTitle {
    width: 130px;
    font-size: 14px;
    // font-weight: 300;
    text-align: right;
    margin: 6px;
    margin-right: 10px;
}

.labelT {
    display: flex;
    width: 70px;
    height: 32px;
    justify-content: center;
    align-items: center;
}

.batchButton {
    display: flex;
    width: 325px;
    justify-content: center;
    align-items: center;
}

.req {
    color: rgb(245, 0, 0);
    margin-right: 4px;
}
</style>
