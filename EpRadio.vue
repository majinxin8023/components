<!-- 暂未使用
el-radio-group
@author Mr.ma
@use    单选框
@param  epdata  业务对象。
@param  meta 当前组件元数据，支持属性有：
        其中的子集参数 {disabled,text-color,fill,label,border,size} 如meta.placeholder 见element-ui2.0api
-->
<template>
  <el-radio-group
    v-model="epdata[meta.name]"
    :disabled="meta.disabled"
    :text-color="meta.textColor"
    :fill="meta.fill">
    <el-radio v-for="ele in meta.options" 
      :label="ele.label"
      :key="ele.label"
      :disabled="ele.currentDisabled"
      :border="ele.border"
      :size="ele.size"
    >
    </el-radio>
  </el-radio-group>
</template>
<script>
export default {
    name: "ep-radio",
    props: {
    epdata: {
      type: Object,
      default: () => {} // 默认为空对象
    },
    meta: {
      type: Object,
      default: () => {} // 默认为空对象
    }
  },
}
</script>
<style>
</style>
