<template>
	<div>
		<el-upload ref="uploadImg" action="" list-type="picture-card" style="width: 90%">
			<i class="el-icon-plus"></i>
		</el-upload>
	</div>
</template>

<script>
import { uploadOSS } from '@/apiUtils'
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
	methods: {
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
		}
	}
}
</script>

<style scoped></style>
