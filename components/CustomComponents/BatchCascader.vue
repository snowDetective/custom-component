<!-- 普通选择器批量导入 -->
<template>
    <div class="batch">
        <BatchImportFieldAll :mapData="mapData" :options="options" :searchDesc="searchDesc" :modelURL="downloadModel"
            @preInspection="preInspection" numFmt="@">
        </BatchImportFieldAll>
    </div>
</template>

<script>
import { MessageBox } from 'element-ui'
import BatchImportFieldAll from '@/sharedComponents/BatchImportFieldAll'
// import { importMaterial } from '@/apiUtils'
export default {
    components: { BatchImportFieldAll },
    data() {
        return {
            downloadModel: `https://hl-security.oss-cn-shenzhen.aliyuncs.com/excel/mainten/级联选择器导入模板.xlsx`,
            msg: '', // 错误信息
            mapData: [],
            options: {},
            searchDesc: '请输入关键字'
        }
    },

    methods: {
        // 预检查与处理
        preInspection(ctx) {
            const { data, showError, updateData, addError } = ctx
            let flag = false
            const startTimer = Date.now()
            const loading = this.$loading({
                lock: true,
                text: '正在检验数据基本格式',
                spinner: 'el-icon-loading'
            })

            for (const item of data) {
                for (let i = 0; i < item.length; i++) {
                    // undefined 向用户提示错误
                    const el = item[i]
                    if (el === void 0) {
                        flag = true
                        updateData(item.$excelIndex, '$state', -1)
                        updateData(item.$excelIndex, '$msg', '该行中间存在空值')
                        addError(item)
                    }
                }
            }

            // 关闭 loading
            const endTimer = Date.now()
            const difference = 500
            if (endTimer - startTimer >= difference) {
                loading.close()
            } else {
                setTimeout(() => {
                    loading.close()
                }, difference - (endTimer - startTimer))
            }

            if (flag) {
                showError()
            }


            // 去除数组中无用数据
            function cleanData(data) {
                return data.map(item => {
                    const cleanedItem = {};
                    for (let i = 0; i < item.length; i++) {
                        cleanedItem[i] = item[i];
                    }
                    return Object.values(cleanedItem);
                });
            }

            // const options = cleanData(data)
            // console.log(options);

            let idCounter = 1;

            // 对数组进行预检,是否有相同父,子级
            function addItem(target, keys) {
                // 当前处理的层级
                let current = target;
                let parentStack = [];
                // let id = idCounter++
                for (let i = 0; i < keys.length; i++) {
                    const key = keys[i];
                    // 寻找该层级是否有与之前父,子级相同的数据
                    let followItem = current.find(item => item.name === key);

                    if (!followItem) {
                        followItem = {
                            id: idCounter++,
                            name: key
                        };
                        if (i < keys.length - 1) {
                            followItem.children = [];
                        }
                        current.push(followItem);
                    }
                    parentStack.push({ parent: current, item: followItem });
                    current = followItem.children;
                }
                // 删除空的 children 属性
                for (let i = parentStack.length - 1; i >= 0; i--) {
                    const { parent, item } = parentStack[i];
                    if (item.children && item.children.length === 0) {
                        delete item.children;
                    }
                }
            }

            // 发送给后端
            function optionItem(keysArray) {
                return keysArray.map(keys => {
                    return keys.map(key => ({
                        name: key
                    }));
                });
            }

            const cleanedData = cleanData(data);

            const result = [];

            cleanedData.forEach(item => {
                addItem(result, item, idCounter);
            });

            const options = optionItem(cleanedData);

            // this.cascader = result
            this.$emit('cascader', result, options);
            // console.log(JSON.stringify(result, null, 2));
        },

    }
}
</script>

<style scoped lang="scss">
@import '~@/style/global.scss';

.batch {
    display: flex;
    margin-top: -10px;
}
</style>
