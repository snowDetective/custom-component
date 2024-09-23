<template>
    <div>
        <!-- <label>{{ data.label }}</label> -->
        <el-select v-model="sValue" multiple style="width: 100%;" clearable :placeholder="parentData.placeholder" v-if="parentData.componentType === 'create'">
            <el-option v-for="item in parentData.list" :key="item.id" :value="item.id" :label="item.name"></el-option>
        </el-select>
        <el-select v-model="sValue" multiple style="width: 100%;" clearable :placeholder="parentData.placeholder" v-if="parentData.componentType !== 'create'">
            <el-option v-for="item in parentData.options" :key="item.id" :value="item.id" :label="item.name"></el-option>
        </el-select>
    </div>
</template>

<script>
export default {
    props: {
        parentData: {
            // type: string,
            required: true
        }
    },
    data() {
        return {
            selectValue: [],
            sValue: []
        }
    },
    methods: {
        // updateValue() {
        //     console.log(this.data);
        // }
    }
}
</script>

<style scoped></style>