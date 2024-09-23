<template>
    <div>
        <el-date-picker v-model="value" type="date" :placeholder="parentData.placeholder" style="width: 100%;">
        </el-date-picker>
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
            type: Object,
            required: true,
            value: ''
        }
    },
}
</script>

<style scoped></style>