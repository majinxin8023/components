<!--
el-input-number
@author Mr.ma
@use    文本框计数器
@param  epdata  业务对象，例如工作量planWorkload，配合meta.name将工作量生成为自动完成框。
@param  meta 当前组件元数据，支持属性有：
        其中的子集参数 {min,max,step,disabled,controls,controls-position,size,label} 如meta.placeholder 见element-ui2.0api
        其中自定义 { uri(事件发起请求的uri路径)}
-->
<template>
  <el-input-number
    v-model="epdata[meta.name]"
    :min="meta.min"
    :max="meta.max"
    :step="meta.step"
    :disabled="meta.disabled"
    :controls="meta.controls"
    :controls-position="meta.controlsPosition"
    :size="meta.size"
    :label="meta.label"
    @change="handleChange"
  >
    <template v-if="meta.prepend != null" slot="prepend">
      <i style="font-size:16px;" :class="meta.prepend"></i>
    </template>
  </el-input-number>
</template>
<script>
export default {
  name: 'ep-number',
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
  methods: {
    handleChange (value) {
      let oldVal = this.epdata[this.meta.name]
      this.$emit('change-event', {oldVal, curVal: value, meta: this.meta})
    }
  }
}
</script>
