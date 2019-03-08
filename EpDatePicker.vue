<!--
ep-date-picker
@use    日期选择控件
@author Mr.ma
@param  epdata  业务对象，例如日期requireDate，配合meta.name将日期生成为自动完成框。
@param  meta 当前组件元数据，支持属性有：
        其中的子集参数 {type,size,readonly,disabled,editable,format,clearable,placeholder,prefix-icon,clear-icon,start-placeholder,end-placeholder,range-separator,unlink-panels} 如meta.placeholder 见element-ui2.0api
        其中自定义 {
            width: int 显示长度,
            col: number 具体占位(结合element的row和col组件,进行布局),
            uri(页面发起请求的路径)
            meaning(具体页面的业务逻辑)}
@event  changeDate 输入框的blur事件触发，交给页面添加业务逻辑
-->
<template>
  <el-date-picker
    v-model="epdata[meta.name]"
    :type="meta.type"
    :size="meta.size"
    :readonly="meta.readonly"
    :disabled="meta.disabled"
    :editable="meta.editable"
    :format="meta.format"
    :clearable="meta.clearable"
    :placeholder="$t(meta.placeholder)"
    :prefix-icon="meta.prefixIcon"
    :clear-icon="meta.clearIcon"
    :value-format="meta.valueFormat"
    :start-placeholder="meta.startPlaceholder"
    :end-placeholder="meta.endPlaceholder"
    :range-separator="meta.rangeSeparator"
    :unlink-panels="meta.unlinkPanels"
    @change="changeDate"
    @focus="focusData"
    >
  </el-date-picker>
</template>
<script>
export default {
  name: 'ep-date-picker',
  data () {
    return {
      oldVal: ''
    }
  },
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
    /**
     * 光标进入，保存之前老值
     * @author Mr.ma
     */
    focusData () {
      this.oldVal = this.epdata[this.meta.name]
    },
    /**
     * change事件给后台提交请求
     * @author Mr.ma
     * @use 编辑日期
     */
    changeDate () {
      this.$emit('change-event', {oldVal: this.oldVal, curVal: this.epdata[this.meta.name], meta: this.meta})
    }
  }
}
</script>
