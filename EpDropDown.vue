<!--
ep-dropdown
@use    点击下拉菜单显示
@author Mr.ma
@param  epdata  业务对象，例如优先级priorityLevel，配合meta.name将优先级生成为自动完成框。
@param  meta 当前组件元数据，支持属性有：
        其中的子集参数 {trigger,type,split-button,placement,hide-on-click,show-timeout,hide-timeout} 如meta.placeholder 见element-ui2.0api
        其中自定义 {
            uri(传递到页面的uri路径)
            meaning(具体页面的业务逻辑)}
@event  handlecommand 点击菜单项触发回调  参数command 当前选中值的下标
-->
<template>
  <el-dropdown @command="handlecommand"
  :trigger="meta.trigger"
  :type="meta.type"
  :split-button="meta.splitButton"
  :placement="meta.placement"
  :hide-on-click="meta.hideOnClick"
  :show-timeout="meta.showTimeout"
  :hide-timeout="meta.hideTimeout">
    <div class="el-dropdown-link">
      <el-button round class="btn" size="mini" :disabled="meta.disabled">
        <i class="el-icon-ion-social-buffer buffer"></i>
        <span>{{$t(epdata[meta.name])}}</span>
      </el-button>
    </div>
    <el-dropdown-menu slot="dropdown">
      <el-dropdown-item v-for="(item, index) in meta.datas" :command="index" :key="item.id">
        <i class="el-icon-ion-social-buffer"></i>
        {{$t(item.value)}}
      </el-dropdown-item>
    </el-dropdown-menu>
  </el-dropdown>
</template>
<script>
export default {
  name: 'ep-drop-down',
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
     * element自带方法，点击下拉菜单触发
     * @param command 当前选中值的下标
     * @author Mr.ma
     * @use 下拉菜单
     */
    handlecommand (command) {
      let oldVal = this.epdata[this.meta.name]
      this.epdata[this.meta.name] = this.meta.datas[command].value
      this.$emit('change-event', {oldVal, curVal: this.$t(this.epdata[this.meta.name]), meta: this.meta}) // oldVal 老值 currentValue 改变后的值 meta 当前meta信息
    }
  }
}
</script>
<style scoped>
.el-dropdown-link {
  cursor: pointer;
  display:inline-block;
}
.el-icon-arrow-down {
  font-size: 16px;
}
.buffer{
  font-size:16px;
}
</style>
