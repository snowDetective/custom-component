<template>
	<div>
		<el-cascader v-model="cascaderValue" :options="parentData.list" style="width: 100%" :props="cascaderProps"
			v-if="parentData.componentType === 'create'" clearable :placeholder="parentData.placeholder"></el-cascader>
		<el-cascader v-model="createValue" :options="parentData.options" style="width: 100%" :props="createProps"
			v-if="parentData.componentType !== 'create'" clearable :placeholder="parentData.placeholder"></el-cascader>
	</div>
</template>

<script>
import { getCascader, getModuleCascader } from '@/apiUtils'
export default {
	props: {
		parentData: {
			// type: string,
			required: true
		}
	},
	data() {
		return {
			cascaderProps: {
				value: 'id',
				label: 'name',  // 将 label 字段改为 name
				children: 'children',
				multiple: true
			},
			cascaderValue: '',
			createValue: '',
			projectId: '',
			createProps: {
				value: 'id',
				label: 'name',  // 将 label 字段改为 name  
				children: 'children',
				multiple: true,
				lazy: true,
				checkStrictly: true,
				lazyLoad: async (node, resolve) => {
					try {
						if (node.data.template) {
							const id = node.data ? node.data.id : null;
							// 调用接口获取子级数据
							// 发送的selfId是父级id,id是组件模版id
							const { data = [] } = await getModuleCascader({ selfId: id, id: this.parentData.templateId, typeId: this.parentData.typeId });
							// 动态加载子级
							resolve(data);
						} else {
							const id = node.data ? node.data.id : null;
							// 调用接口获取子级数据
							const { data = [] } = await getCascader({ id: id, projectId: this.parentData.projectId, formId: this.parentData.typeId });
							// 动态加载子级
							resolve(data);
						}
					} catch (error) {
						console.error(error);
					}
				}
			}
		}
	},
	methods: {}
}
</script>

<style scoped></style>
