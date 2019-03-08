<!--
ep-form
@use    form表单
@author Mr.ma
@param  epdata  业务对象，当前from表单传入的数据
@param  meta 当前组件元数据，通过判断meta传入的type值，生成一个from表单
@event  submitForm 点击确定按钮，进行数据提交，页面使用该组件去监听，拿取数据，页面完成相对应业务逻辑
        cancel 点击取消按钮，页面使用该组件去监听，拿取数据，页面完成相对应业务逻辑
-->
<template>
  <el-form class="form-data" ref="formData" :model="formData" :inline="inline" :rules="rules">
    <el-row v-for="(newRowIndex,i) in metasRowIndexs" :key="newRowIndex" :gutter="20" ref="row" style="margin-left:5px;margin-right:0px">
      <el-col v-for="meta in metas.slice(newRowIndex, (metasRowIndexs[i+1] || metas.length))" :key="meta.name" :lg="meta.lg" :xl="meta.xl">
        <el-form-item class="form-item" :label="meta.label" :prop="meta.name" ref="formItem">
          <template v-if="isType(meta,['text','textarea'])">
            <ep-text :meta="meta" :epdata="formData"/>
          </template>
          <template v-else-if="isType(meta,['select'])">
            <ep-select :meta="meta" :epdata="formData"></ep-select>
          </template>
          <template v-else-if="isType(meta,['date'])">
            <ep-date-picker :meta="meta" :epdata="formData"></ep-date-picker>
          </template>
          <template v-else-if="isType(meta,['autoComplete'])">
            <ep-auto-complete :forceSelect="forceSelect" v-on:change-event="itemChangeEventHander" :meta="meta" :epdata="formData"></ep-auto-complete>
          </template>
          <template v-else-if="isType(meta,['number'])">
            <ep-number :meta="meta" :epdata="formData"></ep-number>
          </template>
          <template v-else-if="isType(meta,['richeditor'])">
            <ep-rich-text :meta="meta" :epdata="formData" ref="richeditor"></ep-rich-text>
          </template>
          <template v-else-if="isType(meta,['upload'])">
            <ep-upload :meta="meta" ref="upload" :epdata="formData"></ep-upload>
          </template>
        </el-form-item>
      </el-col>
    </el-row>
    <slot></slot>
  </el-form>
</template>
<script>
import epSelect from './EpSelect.vue'
import epText from './EpText.vue'
import epAutoComplete from './epAutoComplete.vue'
import epDatePicker from './EpDatePicker.vue'
import epNumber from './EpNumber.vue'
import epRichText from './EpRichText.vue'
import epUpload from './EpUpload.vue'
export default {
  name: 'ep-form',
  data () {
    return {
      metasRowIndexs: [], // 根据metas中的newRow=true标记进行换行显示
      initMetas: []
    }
  },
  props: {
    formData: {
      type: Object,
      default: () => {}
    },
    inline: {
      type: Boolean,
      default: true
    },
    rules: {
      type: Object,
      default: () => {}
    },
    forceSelect: {
      type: Boolean,
      default: false
    },
    metas: {
      type: Array,
      default: () => []
    }
  },
  components: {
    epText, epSelect, epDatePicker, epNumber, epRichText, epUpload, epAutoComplete
  },
  watch: {
    /**
     * 按标志位找出多个分行点
     */
    metas: {
      handler: function (newVal, oldVal) {
        this.initMetas = newVal
        if (this.metasRowIndexs.length > 0) {
          this.metasRowIndexs.splice(0, this.metasRowIndexs.length)
        }
        for (let i in newVal) {
          if (i === 0 || newVal[i].newRow) {
            this.metasRowIndexs.push(i)
          }
        }
      },
      immediate: true
    }
  },
  methods: {
    /**
     * 表单校验
     * @param data 当前选中项和meta信息
     * @use 提供父组件进行表单校验
     */
    validataForm () {
      return this.$refs.formData.validate()
    },
    /**
     * 当前下拉菜单中的具体一项
     * @param data 当前选中项和meta信息
     * @author Mr.ma
     * @use 选中某条信息之后的附带信息，传递给页面自己处理
     */
    itemChangeEventHander (event) {
      this.$emit('item-change', event) // 提交给页面
    },
    /**
     * 判断meta的type是否在types中
     * @param meta 传入的meta信息
     * @param types 使用组件传入type值
     * @author Mr.ma
     * @use 组件渲染
     */
    isType (meta, types) {
      var flag = false
      for (var i in types) {
        if (types[i] === meta.type) {
          flag = true
          break
        }
      }
      return flag
    }
  },
  mounted () {
    this.$refs.richeditor[0].$parent.$parent.$el.style.marginBottom = '10px' // 设置富文本编辑器的高度自适应
    this.$refs.richeditor[0].$parent.$parent.$parent.$el.style.height = '45%'
    this.$refs.richeditor[0].$parent.$parent.$el.style.height = '100%'
    this.$refs.richeditor[0].$parent.$el.style.width = '98%'
    this.$refs.richeditor[0].$parent.$el.style.height = '100%'
    this.$refs.upload[0].$parent.$el.style.height = '50px'
    this.$refs.formItem.forEach((ele, index) => { // 此循环是为了让input根据meta传入的col值来进行自适应
      if (ele.$children[0]) {
        ele.$children[0].$el.style.width = '100%'
      }
    })
  }
}
</script>
<style scoped>
.form-item{
  width:100%;
}
</style>
<style>
/* rule验证失败 */
.el-form-item__error{
  padding-top:0px;
}
.el-form--inline .el-form-item__content{
  width:100%;
  height:100%;
}
.el-form-item{
  margin-bottom:0;
}
.el-date-picker{
  width:278px;
}
.form-data{
  min-width: 920px;
}
.el-picker-panel__body .el-picker-panel__content{
  width:250px;
}
.form-data .el-input-group__append, .el-input-group__prepend{
  height:23px;
  padding:1px;
  border-top-right-radius: 4px;
  border-bottom-right-radius: 4px;
}
.form-data .el-autocomplete .el-input .el-input__inner{
  height:28px;
  line-height:28px;
  font-size:smaller;
}
</style>
