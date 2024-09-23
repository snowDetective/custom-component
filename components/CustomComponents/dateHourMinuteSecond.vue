<template>
    <el-time-picker v-model="value1" :placeholder="parentData.placeholder">
    </el-time-picker>
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
            value1: new Date(),
        };
    }
}
</script>