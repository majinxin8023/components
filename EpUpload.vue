<!--
ep-upload
@author Mr.ma
@use  图片上传
@param  epdata  业务对象,例如upload，配合meta.name将字符串json生成为不同icon进行展示页面。
        meta.name结构   eg:"{\"name\":\"xx.doc\",\"id\":\"11\",\"extName\":\"doc\"},{\"name\":\"yy.doc\",\"id\":\"22\",\"extName\":\"xlsx\"}"
@param  meta 当前组件元数据，支持属性有：
        其中的子集参数 {headers,multiple,data,drag,limit,auto-upload,with-credentials,show-file-list,disabled,file-list}} 如meta.placeholder 见element-ui2.0api
        其中自定义 {
          col: number 具体占位(结合element的row和col组件,进行布局)
        }
@event  handleUploadSuccess 事件1 图片上传成功 参数详见element
@event  handleExceed  事件2 图片上传超过个数
@event  handleFileRemove  事件3 删除附件 参数item  当前附件信息
@event  handleExceed  事件4 下载附件 参数item  当前附件信息
@event  checkImages  事件5 查看大图 参数item  当前附件信息
-->
<template>
<div class="upload-box">
  <div class="upload-list" v-for="item in defaultUploadList" :key="item.name">
    <template v-if="item.status === 'finished'">
      <el-tooltip class="tooltip" :content="item.name" placement="top">
        <div style="width:50px;height:50px">
          <div>
            <p v-if="!down">
              <a :href="showFileIcon(item)"></a>
              <img class="imgs" :src="showFileIcon(item)">
            </p>
            <img v-if="down" class="imgs" :src="showFileIcon(item)">
          </div>
          <div class="upload-list-cover">
            <i v-if="item.extName ==='jpg' || item.extName ==='png' || item.extName ==='gif' || item.extName ==='jpeg'" class="el-icon-zoom-in" @click="checkImages(item)"></i>
            <i v-else class="el-icon-ion-ios-cloud-download-outline" @click="handleFileView(item)"></i>
            <i class="el-icon-ion-ios-trash-outline" @click="handleFileRemove(item)"></i>
          </div>
          <el-dialog :visible.sync="dialogVisible">
            <img class="dialog-image-url" :src="dialogImageUrl" alt="">
          </el-dialog>
        </div>
      </el-tooltip>
    </template>
    <template v-else>
      <el-progress :percentage="item.percentage" show-text=false></el-progress>
    </template>
  </div>
  <el-upload
    class="upload"
    :action="uploadServerAction"
    :multiple="meta.multiple"
    :headers="meta.headers"
    :data="meta.data"
    :drag="meta.drag"
    :limit="meta.limit"
    :auto-upload="meta.autoUpload"
    :with-credentials="meta.withCredentials"
    :show-file-list="meta.showFileList"
    :disabled="meta.disabled"
    :file-list="defaultUploadList"
    :on-success="handleUploadSuccess"
    :on-exceed="handleExceed"
    style="display: inline-block;width:48px;">
    <div style="width: 48px;height:48px;line-height: 48px;border:1px dashed #eee;margin-top:1px">
      <i class="el-icon-ion-upload upload-icon"></i>
    </div>
  </el-upload>
</div>
</template>
<script>
import {http} from '@/services/config'
export default {
  name: 'ep-upload',
  data () {
    return {
      dialogVisible: false, // 显示大图的变量
      defaultUploadList: [], // 对于文件的初始化
      uploadServerAction: '/api/uploadFile', // action地址
      dialogImageUrl: '', // 大图url路径
      initValue: '', // 初始数据
      down: true // 下载把icon改变a标签
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
    },
    handleFail: { // 为true回退
      type: Boolean,
      default: false
    }
  },
  watch: {
    handleFail (newVal, oldVal) {
      this.epdata[this.meta.name] = this.initValue
      this.defaultUploadList = []
      if (this.epdata[this.meta.name]) { // 初始化附件
        var dataFileList = (this.epdata[this.meta.name])
        dataFileList = JSON.parse('[' + (dataFileList).replace(/'/g, '"') + ']')
        for (var i in dataFileList) {
          var f = dataFileList[i]
          f.url = '/api/downFile/' + f.id // 添加下载链接
          f.status = 'finished'
          this.defaultUploadList.push(f)
        }
      }
    }
  },
  created () {
    this.initFile()
  },
  methods: {
    initFile () {
      if (this.epdata[this.meta.name]) { // 初始化附件
        this.defaultUploadList = []
        this.initValue = this.epdata[this.meta.name]
        var dataFileList = (this.epdata[this.meta.name])
        dataFileList = JSON.parse('[' + (dataFileList).replace(/'/g, '"') + ']')
        for (var i in dataFileList) {
          var f = dataFileList[i]
          f.url = '/api/downFile/' + f.id // 添加下载链接
          f.status = 'finished'
          this.defaultUploadList.push(f)
        }
      }
    },
    /**
     * 显示不同icon
     * @author Mr.ma
     * @use 对于上传文件的后缀名做解析，改变成不同的icon显示
     * @return icon
     */
    showFileIcon (item) {
      this.down = true
      var extName = item.extName.toLowerCase()
      var icon = require('../assets/txt.png')
      if (extName === 'doc' || extName === 'docx') {
        icon = require('../assets/word.png')
      } else if (extName === 'xls' || extName === 'xlsx') {
        icon = require('../assets/excel.png')
      } else if (extName === 'ppt' || extName === 'pptx') {
        icon = require('../assets/ppt.png')
      } else if (extName === 'pdf') {
        icon = require('../assets/pdf.png')
      } else if (extName === 'zip' || extName === 'rar') {
        icon = require('../assets/zip.png')
      } else if (extName === 'png' || extName === 'gif' || extName === 'jpg' || extName === 'jpeg') {
        icon = item.url
      }
      return icon
    },
    /**
     * 图片查看大图
     * @author Mr.ma
     * @use 图片的大图查看
     */
    checkImages (item) {
      this.dialogImageUrl = '/api/downFile/' + item.id
      this.dialogVisible = true
    },
    /**
     * 文件移除
     * @author Mr.ma
     * @use 删除附件后续操作
     */
    handleFileRemove (file) {
      let oldFileId = this.epdata[this.meta.name] // 删除之前的老数据
      let fileList = JSON.parse('[' + (this.epdata[this.meta.name]).replace(/'/g, '"') + ']')
      let ind
      fileList.forEach((ele, index) => {
        if (ele.id === file.id) {
          ind = index
        }
      })
      this.defaultUploadList.splice(ind, 1)
      fileList.splice(ind, 1)
      let fileId = JSON.stringify(fileList).replace('[', '').replace(']', '')
      let params = {
        fileId: fileId
      }
      http.get(this.meta.delFileUri.replace(/id/, file.id), params).then(res => {
        if (http.pretreatment(res)) {
          console.log(res.data.msg)
        } else {
          this.epdata[this.meta.name] = oldFileId
          this.initFile()
        }
      })
    },
    /**
     * 附件下载
     * @author Mr.ma
     * @use 点击下载按钮，进行附近下载本地
     */
    handleFileView (file) {
      if (file.extName === 'jpg' || file.extName === 'png' || file.extName === 'gif' || file.extName === 'jpeg') {
        this.down = true
      } else {
        this.down = false
      }
      window.location.href = '/api/downFile/' + file.id
    },
    /**
     * 附件上传
     * @author Mr.ma
     * @use 进行附件上传，对附件做解析
     */
    handleUploadSuccess (response, file, fileList) {
      this.down = true
      if (response.state === '200' && response.data) {
        var fObj = JSON.parse(response.data)
        file.url = '/api/downFile/' + fObj.id
        file.id = fObj.id
        file.extName = fObj.extName
        this.$notify({
          title: '成功',
          message: file.name + '上传成功',
          type: 'success'
        })
        this.makeUploadResult(fileList)
      } else {
        this.$notify.error({
          title: '错误',
          message: file.name + '上传失败'
        })
      }
    },
    /**
     * 附件个数超过设置个数
     * @author Mr.ma
     * @use 附件超过个数
     */
    handleExceed (files, fileList) {
      this.$message.warning(`当前限制选择 ${this.epdata[this.meta.limit]} 个文件，本次选择了 ${files.length} 个文件，共选择了 ${files.length + fileList.length} 个文件`)
    },
    /**
     * 生成最终文件到data中显示页面上
     * @author Mr.ma
     * @use 附件格式化
     */
    makeUploadResult (fileList) {
      var files = []
      for (var i in fileList) {
        var f = fileList[i]
        var fJson = {'id': f.id, 'name': f.name, 'extName': f.extName}
        files.push(JSON.stringify(fJson))
      }
      this.epdata[this.meta.name] = files.join(',')
      var addFileList = JSON.parse('[' + (this.epdata[this.meta.name]).replace(/'/g, '"') + ']')
      var lastFileList = addFileList[addFileList.length - 1]
      lastFileList.url = '/api/downFile/' + lastFileList.id // 添加下载链接
      lastFileList.status = 'finished'
      this.defaultUploadList.push(lastFileList)
    }
  }
}
</script>
<style scoped>
.upload-icon{
  font-size:20px;
}
.upload-box{
  min-width:500px;
  height: 50px;
}
.upload-list{
  float: left;
  width: 50px;
  height: 50px;
  text-align: center;
  line-height: 50px;
  border: 1px solid transparent;
  border-radius: 4px;
  overflow: hidden;
  background: #fff;
  position: relative;
  box-shadow: 0 1px 1px rgba(0,0,0,.2);
  margin-right: 4px;
  margin-bottom:20px;
}
.upload-list img{
  width: 100%;
  height: 100%;
}
.tooltip{
  width: 50px;
  height: 50px;
}
.imgs{
  z-index:2;
}
.upload-list-cover{
  display: none;
  z-index:1;
  position: absolute;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
  background: rgba(0,0,0,.6);
}
.upload-list:hover .upload-list-cover{
  display: block;
}
.upload-list-cover i{
  color: #fff;
  font-size: 20px;
  cursor: pointer;
  margin: 0 2px;
}
.upload{
  display:flex;
}
.upload-box .dialog-image-url{
  width:100%;
  height:100%;
}
</style>
<style>
.el-upload-list{
  display:none;
}
.el-progress{
  display:none;
}
</style>
