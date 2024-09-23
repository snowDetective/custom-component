<!-- 级联选择器批量导入 -->
<template>
	<div class="batch">
		<BatchImportFieldAll
			:mapData="mapData"
			:options="options"
			:searchDesc="searchDesc"
			:modelURL="downloadModel"
			@preInspection="preInspection"
			numFmt="@"
			ref="BatchImportFieldAll"
		>
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
			downloadModel: `https://hl-security.oss-cn-shenzhen.aliyuncs.com/excel/mainten/普通选择器导入模板.xlsx`,
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
				return data.map((item) => {
					const cleanedItem = {}
					for (let i = 0; i < item.length; i++) {
						cleanedItem[i] = item[i]
					}
					return Object.values(cleanedItem)
				})
			}

			let idCounter = 1

			// 对数组进行预检,是否有相同父,子级(自己展示)
			function addItem(target, keys, id) {
				// 当前处理的层级
				let current = target
				for (let i = 0; i < keys.length; i++) {
					const key = keys[i]
					let followItem = {}
					followItem = {
						name: key,
						id: id
					}
					current.push(followItem)
				}
			}

			// 对数组进行预检,是否有相同父,子级(发送给后端)
			function optionItem(target, keys) {
				// 当前处理的层级
				let current = target
				for (let i = 0; i < keys.length; i++) {
					const key = keys[i]
					let followItem = {}
					followItem = {
						name: key
					}
					current.push(followItem)
				}
			}

			const cleanedData = cleanData(data)

			const result = []
			// 自己展示
			cleanedData.forEach((item) => {
				addItem(result, item, idCounter)
				idCounter++
			})
			// 传给后端
			const option = []
			cleanedData.forEach((item) => {
				optionItem(option, item)
			})

			this.$emit('select', result, option)
			// console.log(JSON.stringify(option, null, 2));
		}
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
