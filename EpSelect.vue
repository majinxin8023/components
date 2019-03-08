<!--
ep-select
@author Mr.ma
@use    select选择器
@param  epdata  业务对象，例如涉及模块modules，配合meta.name将涉及模块生成为下拉菜单的文本框
@param  meta 当前组件元数据，支持属性有：
        其中的子集参数 {multiple,disabled,value-key,size,clearable,collapse-tags,multiple-limit,auto-complete,placeholder,filterable,allow-create,remote,loading,loading-text,default-first-option,reserve-keyword,popper-append-to-body,remote-method,no-match-text,no-data-text,automatic-dropdown,value,label,disabled} 如meta.placeholder 见element-ui2.0api
        其中自定义 {uri(事件发起请求的uri路径)}
@event  remoteMethod 远程搜索内容 参数element自带 当前值
-->
<template>
  <el-select
    :multiple="meta.multiple"
    :disabled="meta.disabled"
    v-model="epdata[meta.name]"
    :value-key="meta.valueKey"
    :size="meta.size"
    :clearable="meta.clearable"
    :collapse-tags="meta.collapseTags"
    :multiple-limit="meta.multipleLimit"
    :auto-complete="meta.autoComplete"
    :placeholder="$t(meta.placeholder)"
    :filterable="meta.filterable"
    :allow-create="meta.allowCreate"
    :remote="meta.remote"
    :loading="meta.loading"
    :loading-text="meta.loadingText"
    :default-first-option="meta.defaultFirstOption"
    :reserve-keyword="meta.reserveKeyword"
    :popper-append-to-body="meta.popperAppendToBody"
    :remote-method="remoteMethod"
    :no-match-text="meta.noMatchText"
    :no-data-text="meta.noDataText"
    :automatic-dropdown="meta.automaticDropdown"
    @change="changeSearchObj"
  >
    <!-- 有valueKey表示为绑定的为对象(element官方提供),做判断是为了可传入对象/某个值 -->
    <el-option
      v-show="meta.valueKey"
      v-for="item in meta.options"
      :key="item.id"
      :label="$t(item.name)"
      :value="$t(item)"
      :disabled="item.disabled">
    </el-option>
    <el-option
      v-show="!meta.valueKey"
      v-for="item in meta.options"
      :key="$t(item.name)"
      :label="$t(item.label)"
      :value="item.name"
      :disabled="item.disabled">
    </el-option>
  </el-select>
</template>
<script>
import {http} from '@/services/config'
export default {
  name: 'ep-select',
  data () {
    return {
      options: [],
      loading: false
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
     * element自带方法,远程发起请求
     * @author Mr.ma
     * @use 实现select的远程搜索
     */
    remoteMethod (query) {
      this.loading = true
      if (query !== '') {
        this.loading = false
        let params = {
          key: query
        }
        http.get(this.meta.uri, params).then(res => {
          if (http.pretreatment(res)) {
            this.options = res.data.data
          } else {
            this.options = []
          }
        })
      }
    },
    /**
     * change事件，要求返回option里面的所有参数
     * @author Mr.ma
     */
    changeSearchObj (item) {
      if (typeof item === 'object') { // 为对象类型才做此事情
        this.epdata[this.meta.name] = item
      }
    }
  }
}
</script>
