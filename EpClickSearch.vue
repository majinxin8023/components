<!--
ep-click-search
@use    点击进行远程搜索
@author Mr.ma
@param  epdata  业务对象，例如人员Person，配合meta.name将人员姓名生成为自动完成框。
@param  meta 当前组件元数据，支持属性有：
        其中的子集参数 {disabled,placement} 如meta.placeholder 见element-ui2.0api
        其中自定义 {
            uri(传递到页面的uri路径)
            searchUri(input框远程搜索内容)}
        默认参数{trigger(只支持点击事件)}
@event  sendMethods 用户点击将值赋值给this.epdata[this.meta.name]
        get 参数($event) 事件对象 获取数据，用户在input框输入值，进行远程搜索的方法
        appoint 参数index 当前下标 搜索的内容分配到ul里面，点击li的时候选中某个值
-->
<template>
  <div style="display:inline-block;">
    <el-popover
      ref="popover"
      v-model="visible"
      :disabled="meta.disabled"
      :placement="meta.placement"
      trigger="click">
      <div class="select">
        <input class="txt" v-if="this.visible" v-focus ref="inputs" type="text" v-model="inputValue" :placeholder="$t(meta.placeholder)" @keyup="get($event)"/>
        <ul class="list" v-show="show">
          <li @click="appoint(index)" style="height:30px;line-height:30px;" :class="{selected: index===defaultPosition}" v-for="(item,index) in serverData" :key="index">{{item.name}}</li>
        </ul>
      </div>
      <el-button round size="mini" :disabled="meta.disabled" @click="edit" slot="reference">
        <i class="el-icon-ion-person person"></i>
        {{$t(this.epdata[this.meta.name])}}
      </el-button>
    </el-popover>
  </div>
</template>
<script>
import {http} from '@/services/config'
export default {
  name: 'ep-click-search',
  data () {
    return {
      serverData: [], // 模糊查询后端给的数据
      show: false, // 无数据不显示ul
      visible: false, // 决定弹出层的显示隐藏
      inputValue: '', // 数据值
      defaultPosition: -1 // 数据显示(无选中)默认位置
    }
  },
  /**
   * 自定义指令
   * @author Mr.ma
   * @use input框的自动获取焦点
   */
  directives: {
    focus: {
      inserted: function (el) {
        el.focus()
      }
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
     * input框显示
     * @param e 当前事件对象
     * @author Mr.ma
     * @use 让input框显示出来，自定义指令才可以加上
     */
    edit () {
      this.visible = true
    },
    /**
     * 键盘抬起事件，把值发送后端
     * @param e 当前事件对象
     * @author Mr.ma
     * @use 获取后台返回的模糊查询的数据和用户操作键盘上下箭头
     */
    get (e) {
      var evt = e || window.event
      var code = evt.which || evt.keyCode
      if (this.inputValue !== '') {
        var params = {
          key: this.inputValue
        }
        http.get(this.meta.uri, params).then(res => {
          if (http.pretreatment(res)) {
            this.serverData = res.data.data
          }
        })
        this.show = true
        if (code === 38) {
          this.defaultPosition--
          if (this.defaultPosition < 0) {
            this.defaultPosition = this.serverData.length - 1
          }
          this.inputValue = this.serverData[this.defaultPosition] && this.serverData[this.defaultPosition].name // 把选中的值赋给input框
        } else if (code === 40) {
          this.defaultPosition++
          if (this.defaultPosition > (this.serverData.length - 1)) {
            this.defaultPosition = 0
          }
          this.inputValue = this.serverData[this.defaultPosition] && this.serverData[this.defaultPosition].name // 把选中的值赋给input框
        } else if (code === 13) {
          this.sendMethods()
        }
      } else {
        this.show = false
        this.defaultPosition = -1
      }
    },
    /**
    * 公用方法
    * @author Mr.ma
    * @use 确定某个值向后台发起编辑请求
    */
    sendMethods () {
      if (this.defaultPosition === -1) {
        this.inputValue = ''
        return
      }
      let oldVal = this.epdata[this.meta.name]
      this.epdata[this.meta.name] = this.inputValue // 赋值
      this.visible = false
      this.$emit('change-event', {oldVal, curVal: this.serverData[this.defaultPosition], meta: this.meta}) // 传递当前选中值和meta信息
      this.inputValue = ''
      this.show = false
    },
    /**
    * 点击某一项li的方法
    * @author Mr.ma
    * @use 改变当前num的值
    */
    appoint (index) {
      this.defaultPosition = index
      this.sendMethods()
    }
  }
}
</script>
<style scoped>
.select{
  max-width:200px;
}
.txt{
  display:inline-block;
  width:135px;
  height:20px;
  padding-left:5px;
  margin:5px;
  outline:none;
  border:1px solid #dce3e8;
}
.list{
  margin-top:5px;
  padding-left:5px;
  min-height:50px;
}
.selected{
  background: rgba(254,115,0,0.05);
}
.person{
  font-size:16px;
  padding-right:5px;
}
</style>
