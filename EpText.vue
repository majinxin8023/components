<!--
el-input
@author Mr.ma
@use    input框
@param  epdata  业务对象，例如涉及模块modules，配合meta.name将涉及模块生成为下拉菜单的文本框
@param  meta 当前组件元数据，支持属性有：
        其中的子集参数 {type,placeholder,type,maxlength,minlength,clearable,disabled,size,rows,autosize,auto-complete,readonly,resize,autofocus,append,prepend} 如meta.placeholder 见element-ui2.0api
-->
<template>
  <el-input
    class="text"
    v-model="epdata[meta.name]"
    :placeholder="$t(meta.placeholder)"
    :type="meta.type"
    :min-width="meta.minWidth"
    :maxlength="meta.maxlength"
    :minlength="meta.minlength"
    :clearable="meta.clearable"
    :disabled="meta.disabled"
    :size="meta.size"
    :rows="meta.rows"
    :autosize="meta.autosize"
    :auto-complete="meta.autoComplete"
    :readonly="meta.readonly"
    :resize="meta.resize"
    :autofocus="meta.autofocus"
    @change="changeInputVal"
  >
    <template v-if="meta.append != null" slot="append">{{$t(meta.append)}}</template>
    <template v-if="meta.prepend != null" slot="prepend">{{$t(meta.prepend)}}</template>
  </el-input>
</template>
<script>
export default {
  name: 'ep-text',
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
    changeInputVal (val) {
      this.$emit('change-event', {curObj: val, meta: this.meta})
    }
  }
}
</script>
