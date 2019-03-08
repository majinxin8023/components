<!--
ep-rich-text
@use    富文本编辑器
@author Mr.ma
@param  epdata  业务对象，例如内容，配合meta.name将富文本编辑器内容生成为自动完成框。
@param  meta 当前组件元数据
-->
<template>
  <div class="editor" style="margin-top:5px;" :type="meta.type">
    <div :id="meta.name + 'id'" class="toolbar"></div>
    <div :id="meta.name + 'ct'" class="text"></div>
  </div>
</template>
<script>
import WangEditor from 'wangeditor'
// import {servIp} from '@/services/config'
export default {
  name: 'ep-rich-text',
  data () {
    return {
      // uploadServerAction: servIp + '/uploadfile',
      editor: Object
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
  mounted () {
    let that = this // 这个地方传入div元素的id 需要加#号
    this.editor = new WangEditor('#' + this.meta.name + 'id', '#' + this.meta.name + 'ct')
    this.editor.customConfig.menus = [
      'bold', // 粗体
      'italic', // 斜体
      'underline', // 下划线
      'foreColor', // 文字颜色
      'link', // 插入链接
      'list', // 列表
      'table', // 表格
      'justify', // 对齐方式
      'code' // 插入代码
    ]
    this.editor.customConfig.uploadImgServer = '/uploadImg'
    this.editor.customConfig.pasteIgnoreImg = true // 忽略粘贴内容中的图片
    this.editor.customConfig.showLinkImg = false // 取消插入图片链接功能
    this.editor.customConfig.uploadImgHooks = {
      customInsert: function (insertImg, result, editor) {
        var url = result.data
        insertImg(url)
      }
    }
    this.editor.customConfig.onchange = function (html) {
      that.epdata[that.meta.name] = html
    }
    this.editor.customConfig.zIndex = 2
    this.editor.create() // 生成编辑器
    if (this.epdata[this.meta.name]) {
      this.editor.txt.html(this.epdata[this.meta.name])
    }
  }
}
</script>
<style scoped>
.editor{
  height:100%;
}
.toolbar {
  height:20%;
  border-top: 1px solid #ccc;
  border-left: 1px solid #ccc;
  border-right: 1px solid #ccc;
}
.text {
  height:80%;
  border: 1px solid #ccc;
  resize: vertical;
}
</style>
