<template>
  <div>
    <!-- <label>{{ data.label }}</label> -->
    <el-input v-model="parentData.value" type="textarea" @input="updateValue"
      :placeholder="parentData.placeholder"></el-input>
  </div>
</template>

<script>
export default {
  props: {
    parentData: {
      // type: string,
      required: true,
      value: ''
    }
  },
  methods: {
    updateValue(event) {
      this.$emit('update:value', event.target.value);
      // console.log(event.target.value);
    }
  }
};
</script>



<style scoped></style>