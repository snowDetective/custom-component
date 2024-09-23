<template>
    <div>
        <div>
            <el-card class="box-card">
                <div slot="header" class="clearfix">
                    <span>组件选择</span>
                </div>
                <el-form label-position="right" label-width="100px">
                    <el-form-item label="输入框:">
                        <div style="display: flex">
                            <el-input placeholder="请输入" v-model="cardInput"></el-input>
                            <el-button class="add" @click="assemblyAdd('inputBox')" icon="el-icon-plus"></el-button>
                        </div>
                    </el-form-item>
                    <el-form-item label="选择器:">
                        <div style="display: flex">
                            <el-select placeholder="请选择" style="width: 100%" v-model="cardSelect">
                                <el-option v-for="item in cardSelects" :key="item.value" :label="item.label"
                                    :value="item.value"></el-option>
                            </el-select>
                            <el-button class="add" @click="assemblyAdd('choose')" icon="el-icon-plus"></el-button>
                        </div>
                    </el-form-item>
                    <el-form-item label="级联选择器:">
                        <div style="display: flex">
                            <el-cascader placeholder="请选择" style="width: 100%" :options="cardCascader"></el-cascader>
                            <el-button class="add" @click="assemblyAdd('cascader')" icon="el-icon-plus"></el-button>
                        </div>
                    </el-form-item>
                    <el-form-item label="时间选择器:">
                        <div style="display: flex">
                            <el-date-picker placeholder="选择日期时间" type="month" value-format="timestamp"
                                style="width: 100%" v-model="cardDate">
                            </el-date-picker>
                            <el-button class="add" @click="assemblyAdd('dateYearMonth')"
                                icon="el-icon-plus"></el-button>
                        </div>
                    </el-form-item>
                    <el-form-item label="上传图片:">
                        <div style="display: flex; align-items: center">
                            <el-upload ref="uploadImg" action="" list-type="picture-card" style="width: 90%">
                                <i class="el-icon-plus"></i>
                            </el-upload>
                            <el-button class="upAdd" @click="assemblyAdd('pictures')" icon="el-icon-plus"></el-button>
                        </div>
                    </el-form-item>
                    <el-form-item label="上传视频:">
                        <div style="display: flex; align-items: center">
                            <el-upload ref="uploadImg" action="" list-type="picture-card" style="width: 90%">
                                <i class="el-icon-plus"></i>
                            </el-upload>
                            <el-button class="upAdd" @click="assemblyAdd('movie')" icon="el-icon-plus"></el-button>
                        </div>
                    </el-form-item>
                </el-form>
            </el-card>
        </div>

        <!-- 创建添加组件 -->
        <el-dialog :visible.sync="add.drawer" width="55%" :before-close="handleClose" destroy-on-close append-to-body>
            <el-form label-position="right" label-width="110px" style="margin-top: 20px">
                <el-form-item label="已选组件:">
                    <el-select v-model="add.data.type" placeholder="请选择组件" disabled style="width: 100%">
                        <el-option v-for="item in comList" :key="item.index" :label="item.name" :value="item.type">
                        </el-option>
                    </el-select>
                </el-form-item>
            </el-form>
            <el-form label-position="right" label-width="110px">
                <el-form-item label="组件标签名:">
                    <el-input v-model="add.data.title" placeholder="请输入组件标签名"></el-input>
                </el-form-item>
            </el-form>
            <el-form label-position="right" label-width="110px"
                v-if="add.data.type != 'movie' && add.data.type != 'pictures'">
                <el-form-item label="组件提示语:">
                    <el-input v-model="add.data.placeholder" placeholder="请输入组件提示语"></el-input>
                </el-form-item>
            </el-form>
            <el-form label-position="right" label-width="110px"
                v-if="add.data.type == 'inputBox' || add.data.type == 'textField'">
                <el-form-item label="输入框类型:">
                    <el-select v-model="textarea" placeholder="请选择输入框类型" style="width: 100%" @change="comInputType">
                        <el-option v-for="item in textareaList" :key="item.index" :label="item.name" :value="item.type">
                        </el-option>
                    </el-select>
                </el-form-item>
            </el-form>
            <el-form label-position="right" label-width="110px"
                v-if="add.data.type == 'inputBox' || add.data.type == 'textField'">
                <el-form-item label="可输入类型:">
                    <el-select v-model="valueType" placeholder="请选择可输入类型" style="width: 100%">
                        <el-option v-for="item in valueTypeList" :key="item.id" :label="item.name" :value="item.type">
                        </el-option>
                    </el-select>
                </el-form-item>
            </el-form>

            <el-form label-position="right" label-width="110px" v-if="
                add.data.type == 'choose' ||
                add.data.type == 'cascader' ||
                add.data.type == 'selectMultiple' ||
                add.data.type == 'cascaderMultiple'
            ">
                <el-form-item label="选择器类型:">
                    <el-select v-model="add.data.multipleId" placeholder="请选择选择器类型" style="width: 100%" @change="mp">
                        <el-option v-for="item in multipleList" :key="item.id" :label="item.name" :value="item.id">
                        </el-option>
                    </el-select>
                </el-form-item>
            </el-form>
            <el-form label-position="right" label-width="110px" v-if="
                add.data.type == 'dateYearMonth' ||
                add.data.type == 'dateYearMonthDay' ||
                add.data.type == 'dateHourMinuteSecond'
            ">
                <el-form-item label="时间格式:">
                    <el-select v-model="add.data.timeType" placeholder="请选择时间格式" style="width: 100%"
                        @change="getTimeType">
                        <el-option v-for="item in timeList" :key="item.index" :label="item.name" :value="item.timeType">
                        </el-option>
                    </el-select>
                </el-form-item>
            </el-form>
            <el-form label-position="right" label-width="110px">
                <el-form-item label="是否必填:">
                    <el-select v-model="required" placeholder="请选择是否必填" style="width: 100%">
                        <el-option v-for="item in requiredList" :key="item.id" :label="item.name" :value="item.id">
                        </el-option>
                    </el-select>
                </el-form-item>
            </el-form>
            <!-- 普通选择器 -->
            <el-form label-position="right" label-width="110px"
                v-if="add.data.type == 'choose' || add.data.type == 'selectMultiple'">
                <el-form-item label="组件列表上传:">
                    <BatchSelect @select="selectData"></BatchSelect>
                </el-form-item>
            </el-form>
            <!-- 级联 -->
            <el-form label-position="right" label-width="110px"
                v-if="add.data.type == 'cascader' || add.data.type == 'cascaderMultiple'">
                <el-form-item label="级联列表上传:">
                    <BatchCascader @cascader="cascaderData"></BatchCascader>
                </el-form-item>
            </el-form>

            <span slot="footer">
                <el-button @click="add.drawer = false">取 消</el-button>
                <el-button type="primary" @click="addComponent">确 定</el-button>
            </span>
        </el-dialog>
    </div>
</template>

<script>
import { uploadOSS } from '@/apiUtils'
import { notEmpty } from 'assist-tools'
import BatchCascader from './CustomComponents/BatchCascader.vue'
import BatchSelect from './CustomComponents/BatchSelect.vue'
import cascader from './CustomComponents/cascader.vue'
import cascaderMultiple from './CustomComponents/cascaderMultiple.vue'
import inputBox from './CustomComponents/inputBox.vue'
import textField from './CustomComponents/textField.vue'
import choose from './CustomComponents/choose.vue'
import selectMultiple from './CustomComponents/selectMultiple.vue'
import dateYearMonth from './CustomComponents/dateYearMonth.vue'
import dateYearMonthDay from './CustomComponents/dateYearMonthDay.vue'
import dateHourMinuteSecond from './CustomComponents/dateHourMinuteSecond.vue'
import pictures from './CustomComponents/pictures.vue'
import movie from './CustomComponents/movie.vue'
import { fromPairs } from 'lodash-es'
export default {
    components: {
        inputBox,
        choose,
        selectMultiple,
        movie,
        dateYearMonth,
        dateYearMonthDay,
        dateHourMinuteSecond,
        pictures,
        textField,
        cascader,
        cascaderMultiple,
        BatchCascader,
        BatchSelect
    },
    data() {
        return {
            // 校区选择器
            regionList: [],
            // 学校选择器
            schoolList: [],
            order: {
                drawer: false,
                dialog: false,
                data: {}
            },
            typeList: [],
            drawerSize: '48%',
            cardInput: '',
            cardSelect: '',
            // 是否必填
            requiredList: [
                { id: '1', type: true, name: '必填' },
                { id: '2', type: false, name: '可选' }
            ],
            required: '1',
            // 数据类型
            valueTypeList: [
                {
                    id: '1',
                    type: 'string',
                    name: '文本'
                },
                {
                    id: '2',
                    type: 'number',
                    name: '纯数字'
                }
            ],
            valueType: 'string',
            // 是否为文本域
            textareaList: [
                { type: true, name: '文本域' },
                { type: false, name: '普通输入框' }
            ],
            textarea: false,
            cardSelects: [
                {
                    value: 'yizhi',
                    label: '一致'
                },
                {
                    value: 'fankui',
                    label: '反馈'
                },
                {
                    value: 'xiaolv',
                    label: '效率'
                }
            ],
            cardCascader: [
                {
                    value: 'zhinan',
                    label: '展示测试数据',
                    children: [
                        {
                            value: 'shejiyuanze',
                            label: '设计原则',
                            children: [
                                {
                                    value: 'yizhi',
                                    label: '一致'
                                },
                                {
                                    value: 'fankui',
                                    label: '反馈'
                                },
                                {
                                    value: 'xiaolv',
                                    label: '效率'
                                }
                            ]
                        },
                        {
                            value: 'daohang',
                            label: '导航',
                            children: [
                                {
                                    value: 'cexiangdaohang',
                                    label: '侧向导航'
                                },
                                {
                                    value: 'dingbudaohang',
                                    label: '顶部导航'
                                }
                            ]
                        }
                    ]
                }
            ],
            cardDate: '',
            // 组件功能
            adminList: [],
            cycleTimeList: [
                {
                    id: '1',
                    name: '天'
                },
                {
                    id: '2',
                    name: '时'
                },
                {
                    id: '3',
                    name: '分'
                }
            ],
            exemptTimeList: [
                {
                    id: '1',
                    name: '天'
                },
                {
                    id: '2',
                    name: '时'
                },
                {
                    id: '3',
                    name: '分'
                }
            ],
            comList: [
                {
                    type: 'inputBox',
                    name: '输入框'
                },
                {
                    type: 'textField',
                    name: '文本输入框'
                },
                {
                    type: 'choose',
                    name: '选择器(单选)'
                },
                {
                    type: 'selectMultiple',
                    name: '选择器(多选)'
                },
                {
                    type: 'cascader',
                    name: '级联选择器(单选)'
                },
                {
                    type: 'cascaderMultiple',
                    name: '级联选择器(多选)'
                },
                {
                    type: 'dateYearMonth',
                    name: '时间选择器(年月)'
                },
                {
                    type: 'dateYearMonthDay',
                    name: '时间选择器(年月日)'
                },
                {
                    type: 'dateHourMinuteSecond',
                    name: '时间选择器(时分秒)'
                },
                {
                    type: 'pictures',
                    name: '上传图片'
                },
                {
                    type: 'movie',
                    name: '上传视频'
                }
            ],
            timeList: [
                {
                    timeType: 'YY-MM',
                    name: '年-月'
                },
                {
                    timeType: 'YY-MM-DD',
                    name: '年-月-日'
                },
                {
                    timeType: 'HH:mm:ss',
                    name: '时-分-秒'
                }
            ],
            multipleList: [
                {
                    id: 1,
                    // type: false,
                    name: '单选'
                },
                {
                    id: 2,
                    // type: true,
                    name: '多选'
                }
            ],
            // 自定义表单
            component: false,
            components: [],
            dataCom: [],
            assembly: [],
            // 导入的级联内容
            cascader: [],
            // 导入的选择器内容
            selectList: [],
            action: '',
            children: '',
            add: {
                drawer: false,
                data: {
                    multipleId: '',
                    timeType: ''
                }
            }
        }
    },
    created() {
        // this.getAdminInfo()
    },
    methods: {
        cascaderData(result, options) {
            this.cascader = result
            this.add.data.options = options
        },
        selectData(result, option) {
            this.selectList = result
            this.add.data.option = option
        },
        assemblyAdd(val) {
            this.add.data = {}
            this.add.drawer = true
            this.add.data.type = val
            this.required = '1'
        },
        // 创建组件
        addComponent() {
            let newData = {}
            const comClass = {
                // 输入框
                'inputBox': () => {
                    Object.assign(newData.data, { id: 1 })
                },
                // 文本域
                'textField': () => {
                    Object.assign(newData.data, { id: 2 })
                },
                // 选择器
                'choose': () => {
                    newData.data.valueType = 'id'
                    Object.assign(newData.data, {
                        id: 3,
                        options: this.add.data.option,
                    })
                    Object.assign(newData, {
                        list: this.selectList,
                        componentType: 'create'
                    })
                },
                // 多选器
                'selectMultiple': () => {
                    newData.data.valueType = 'id'
                    Object.assign(newData.data, {
                        id: 4,
                        options: this.add.data.option,
                    })
                    Object.assign(newData, {
                        list: this.selectList,
                        componentType: 'create'
                    })
                },
                // 级联器
                'cascader': () => {
                    newData.data.valueType = 'id'
                    Object.assign(newData.data, {
                        id: 5,
                        options: this.add.data.options,
                    })
                    Object.assign(newData, {
                        list: this.cascader,
                        componentType: 'create'
                    })
                },
                // 多选级联
                'cascaderMultiple': () => {
                    newData.data.valueType = 'id'
                    Object.assign(newData.data, {
                        id: 6,
                        options: this.add.data.options,
                    })
                    Object.assign(newData, {
                        list: this.cascader,
                        componentType: 'create'
                    })
                },
                // 日期年月
                'dateYearMonth': () => {
                    newData.data.valueType = 'time'
                    Object.assign(newData.data, {
                        id: 7,
                        timeType: this.add.data.timeType
                    })
                },
                // 日期年月日
                'dateYearMonthDay': () => {
                    newData.data.valueType = 'time'
                    Object.assign(newData.data, {
                        id: 8,
                        timeType: this.add.data.timeType
                    })
                },
                // 日期时分秒
                'dateHourMinuteSecond': () => {
                    newData.data.valueType = 'time'
                    Object.assign(newData.data, {
                        id: 9,
                        timeType: this.add.data.timeType
                    })
                },
                // 上传视频
                'movie': () => {
                    newData.data.valueType = 'movie'
                    Object.assign(newData.data, {
                        id: 10
                    })
                    delete newData.data.placeholder
                },
                // 上传图片
                'pictures': () => {
                    newData.data.valueType = 'picture'
                    Object.assign(newData.data, {
                        id: 11
                    })
                    delete newData.data.placeholder
                },

                // 基础内容(默认)
                'defaultData': () => {
                    newData = {
                        // 传输给后端的组件内容
                        data: {
                            type: this.add.data.type,
                            title: this.add.data.title,
                            placeholder: this.add.data.placeholder,
                            required: this.required,
                            valueType: this.valueType
                        },
                        // 放置外层便于判断
                        type: this.add.data.type,
                        placeholder: this.add.data.placeholder
                    }
                }
            };
            // 操作逻辑
            const operate = (type) => {
                const defaults = comClass['defaultData']
                const addCom = comClass[type]
                defaults()
                addCom()
            }
            // 调用操作
            operate(this.add.data.type)

            if (!notEmpty(newData.data)) {
                this.$message.warning('请确保所有必填参数都填写完整')
                return
            }
            this.$emit('dataCom', newData.data, newData)
            this.add.data = {}
            this.add.drawer = false
        },

        /** 上传图片 */
        async uploadImg(fileInfo) {
            try {
                const { data } = await uploadOSS(fileInfo.file)
                fileInfo.onSuccess(data.url)
                this.order.data.orderImg.push({
                    uid: fileInfo.file.uid,
                    url: data.url
                })
            } catch (error) {
                // console.log(error)
                fileInfo.onError(error)
            }
        },

        /** 删除图片时 */
        removeImgLink(file) {
            const index = this.order.data.orderImg.findIndex((item) => item.uid === file.uid)
            if (index === -1) return
            this.order.data.orderImg.splice(index, 1)
        },

        // 输入框类型
        comInputType() {
            if ((this.add.data.type = 'inputBox' && this.textarea == true)) {
                this.add.data.type = 'textField'
                return
            }
            if ((this.add.data.type = 'textField' && this.textarea == false)) {
                this.add.data.type = 'inputBox'
                return
            }
        },
        // 选择器类型
        mp() {
            if (this.add.data.type == 'choose' && this.add.data.multipleId === 2) {
                this.add.data.type = 'selectMultiple'
                return
            }
            if (this.add.data.type == 'selectMultiple' && this.add.data.multipleId === 1) {
                this.add.data.type = 'choose'
                return
            }
            if (this.add.data.type == 'cascaderMultiple' && this.add.data.multipleId === 1) {
                this.add.data.type = 'cascader'
                return
            }
            if (this.add.data.type == 'cascader' && this.add.data.multipleId === 2) {
                this.add.data.type = 'cascaderMultiple'
                return
            }
        },
        // 时间格式
        getTimeType() {
            if (this.add.data.timeType === 'YY-MM') {
                this.add.data.type = 'dateYearMonth'
                return
            }
            if (this.add.data.timeType === 'YY-MM-DD') {
                this.add.data.type = 'dateYearMonthDay'
                return
            }
            if (this.add.data.timeType === 'HH:mm:ss') {
                this.add.data.type = 'dateHourMinuteSecond'
                return
            }
            // console.log(this.add.data.type);
        },

        handleClose(done) {
            this.$confirm(`未保存的数据将被清空, 确认关闭 ?`, '提示', {
                confirmButtonText: '确定',
                cancelButtonText: '取消',
                type: 'warning'
            })
                .then(() => {
                    done()
                })
                .catch((err) => {
                    if (err instanceof Error) {
                        console.error(err)
                    }
                })
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
    width: 40px;
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




 <!-- addComponent() {
         let newData
         switch (this.add.data.type) {
             case 'inputBox':
                 newData = {
                     type: 'inputBox',
                     data: {
                         id: 1,
                         type: 'inputBox',
                         title: this.add.data.title,
                         placeholder: this.add.data.placeholder,
                         required: this.required,
                         valueType: this.valueType
                     },
                     placeholder: this.add.data.placeholder
                 }
                 break
             case 'textField':
                 newData = {
                     type: 'textField',
                     data: {
                         id: 2,
                         type: 'textField',
                         title: this.add.data.title,
                         placeholder: this.add.data.placeholder,
                         required: this.required,
                         valueType: this.valueType
                     },
                     placeholder: this.add.data.placeholder
                 }
                 break
             case 'choose':
                 newData = {
                     type: 'choose',
                     data: {
                         id: 3,
                         type: 'choose',
                         title: this.add.data.title,
                         placeholder: this.add.data.placeholder,
                         valueType: 'id',
                         required: this.required,
                         options: this.add.data.option
                          multiple: 'false',
                     },
                     list: this.selectList,
                     componentType: 'create',
                     placeholder: this.add.data.placeholder
                 }
                 break
             case 'selectMultiple':
                 newData = {
                     type: 'selectMultiple',
                     data: {
                         id: 4,
                         type: 'selectMultiple',
                         title: this.add.data.title,
                         placeholder: this.add.data.placeholder,
                         valueType: 'id',
                         required: this.required,
                         options: this.add.data.option
                          multiple: 'true',
                     },
                     list: this.selectList,
                     componentType: 'create',
                     placeholder: this.add.data.placeholder
                 }
                 break
             case 'cascader':
                 newData = {
                     type: 'cascader',
                     data: {
                         id: 5,
                         type: 'cascader',
                         title: this.add.data.title,
                         placeholder: this.add.data.placeholder,
                         valueType: 'id',
                         required: this.required,
                         options: this.add.data.options
                          multiple: 'false',
                     },
                     list: this.cascader,
                     componentType: 'create',
                     placeholder: this.add.data.placeholder
                 }
                 break
             case 'cascaderMultiple':
                 newData = {
                     type: 'cascaderMultiple',
                     data: {
                         id: 6,
                         type: 'cascaderMultiple',
                         title: this.add.data.title,
                         placeholder: this.add.data.placeholder,
                         valueType: 'id',
                         required: this.required,
                         options: this.add.data.options
                          multiple: 'true',
                     },
                     list: this.cascader,
                     componentType: 'create',
                     placeholder: this.add.data.placeholder
                 }
                 break
             case 'dateYearMonth':
                 newData = {
                     type: 'dateYearMonth',
                     data: {
                         id: 7,
                         type: 'dateYearMonth',
                         title: this.add.data.title,
                         valueType: 'time',
                         required: this.required,
                         placeholder: this.add.data.placeholder,
                         timeType: this.add.data.timeType
                     },
                     placeholder: this.add.data.placeholder
                 }
                 break
             case 'dateYearMonthDay':
                 newData = {
                     type: 'dateYearMonthDay',
                     data: {
                         id: 8,
                         type: 'dateYearMonthDay',
                         title: this.add.data.title,
                         valueType: 'time',
                         required: this.required,
                         placeholder: this.add.data.placeholder,
                         timeType: this.add.data.timeType
                     },
                     placeholder: this.add.data.placeholder
                 }
                 break
             case 'dateHourMinuteSecond':
                 newData = {
                     type: 'dateHourMinuteSecond',
                     data: {
                         id: 9,
                         type: 'dateHourMinuteSecond',
                         title: this.add.data.title,
                         valueType: 'time',
                         required: this.required,
                         placeholder: this.add.data.placeholder,
                         timeType: this.add.data.timeType
                     },
                     placeholder: this.add.data.placeholder
                 }
                 break
             case 'movie':
                 newData = {
                     type: 'movie',
                     data: {
                         id: 10,
                         type: 'movie',
                         title: this.add.data.title,
                         required: this.required,
                         valueType: 'movie'
                     }
                 }
                 break
             case 'pictures':
                 newData = {
                     type: 'pictures',
                     data: {
                         id: 11,
                         type: 'pictures',
                         title: this.add.data.title,
                         required: this.required,
                         valueType: 'picture'
                     }
                 }
                 break
         }
         if (!notEmpty(newData.data)) {
              console.log(newData.data)
             this.$message.warning('请确保所有必填参数都填写完整')
             return
         }
         this.components.push(newData)
         this.assembly.push(newData)
         this.dataCom.push(newData.data)
         this.$emit('dataCom', newData.data, newData)
          console.log(JSON.stringify(this.dataCom, null, 2));
          console.log(this.components)
         this.add.data.multipleId = ''
         this.add.data.title = ''
         this.add.data.placeholder = ''
         this.add.data.timeType = ''
         this.add.drawer = false
     } -->