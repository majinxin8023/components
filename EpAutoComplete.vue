<!--
ep-autocomplete
@author Mr.ma
@use    文本框,可支持远程搜索,模糊查询
@param  epdata  业务对象，例如人员Person，配合meta.name将人员姓名生成为自动完成框。
@param  meta 当前组件元数据，支持属性有：
        其中的子集参数 {placeholder,disabled,debounce,placement,trigger-on-focus,select-when-unmatched} 如meta.placeholder 见element-ui2.0api
        其中自定义 {uri(事件发起请求的uri路径)}
@event  handleSelect 远程搜索内容，点击选中建议项触发 参数item 当前选中对象
        handleBlur blur事件 若当前选中对象不存在，必须为选中对象，则清空值
        handleFocus focus事件 当前v-model值清空
-->
<template>
  <el-autocomplete
    class="inline-input"
    v-model="epdata[meta.name]"
    :fetch-suggestions="querySearch"
    :disabled="meta.disabled"
    :debounce="meta.debounce"
    :placement="meta.placement"
    :trigger-on-focus="meta.triggerOnFocus"
    :module="meta.modules"
    :hide-loading="meta.hideLoading"
    :selectWhenUnmatched="meta.selectWhenUnmatched"
    :placeholder="$t(meta.placeholder)"
    @select="handleSelect"
    @blur="handleBlur"
    @focus="handleFocus"
  ></el-autocomplete>
</template>
<script>
import {http} from '@/services/config'
export default {
  name: 'ep-autocomplete',
  data () {
    return {
      selectedObj: null // 选中的下拉菜单搜索的值
    }
  },
  props: {
    forceSelect: {
      type: Boolean,
      default: false // 默认为非强制选择
    },
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
     * element自带方法，输入值返回模糊搜索额内容查询
     * @param queryString 当前文本框所输入的值
     * @param callback  回调函数
     * @author Mr.ma
     * @use 远程搜索内容
     */
    querySearch (queryString, callback) {
      let params = {
        key: queryString
      }
      http.get(this.meta.uri, params).then(res => {
        if (http.pretreatment(res)) {
          let data = res.data.data
          for (let i of data) { // autocomplete组件只识别value,所以要把后端返回的值要渲染组件显示的值赋值给value
            i.value = i.name
          }
          callback(data)
        }
      })
    },
    /**
     * element自带方法
     * @param item 当前选中项
     * @author Mr.ma
     * @use 选中某项对象中的值做某些事情
     */
    handleSelect (item) {
      this.selectedObj = item // 当前选中对象
      if (this.forceSelect) {
        this.$emit('change-event', {curObj: this.selectedObj, meta: this.meta})
      }
    },
    /**
     * 下拉菜单内容显示，用户必须选中下拉菜单内容
     * @param evt 事件对象-- 暂未使用该参数
     * @author Mr.ma
     * @use 用户未选中下拉菜单内容，input值为空
     */
    handleBlur (evt) {
      if (this.selectedObj === null) {
        if (this.forceSelect) {
          evt.target.value = ''
          this.epdata[this.meta.name] = ''
        }
      }
    },
    handleFocus (evt) {
      if (evt.target.value) {
        evt.target.value = ''
        this.epdata[this.meta.name] = ''
        this.selectedObj = null
      }
    }
  }
}
</script>
