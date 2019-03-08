<template>
  <div class="search-box">
    <el-dropdown @command="changeType" placement="bottom-end">
      <span class="el-dropdown type" style="border: 0px;padding: 0px 0px 0px 5px;">
        {{exp.label}}
      </span>
      <el-dropdown-menu slot="dropdown">
        <el-dropdown-item v-for="(ele, index) in metas" :command="index" :key="ele.val">{{ele.val}}</el-dropdown-item>
        <el-dropdown-item divided :command="'del'">{{$t('task.delete')}}</el-dropdown-item>
      </el-dropdown-menu>
    </el-dropdown>
    <el-dropdown @command="changeExp" placement="bottom-end">
      <span class="el-dropdown exp">
        {{exp.exp}}
      </span>
      <el-dropdown-menu slot="dropdown">
        <el-dropdown-item v-for="(ele, index) in categoryExp()" :command="index" :key="ele">{{ele}}</el-dropdown-item>
      </el-dropdown-menu>
    </el-dropdown>
    <el-date-picker size="mini" class="date" v-if="this.curmeta.type === 'date'" v-model="exp.val" type="date" :placeholder="$t('task.selectDate')" format="yyyy-MM-dd" value-format="timestamp">
    </el-date-picker>
    <el-input-number size="mini" v-else-if="this.curmeta.type === 'number'" controls-position="right" v-model="exp.val" :step="1"></el-input-number>
    <el-input v-model="exp.val" class="input-text" size="mini" v-else-if="this.curmeta.type === 'string'" :placeholder="$t('task.enterContent')" clearable></el-input>
    <el-select class="select single" value-key="value" collapse-tags v-model="exp.val" v-if="this.curmeta.type === 'select' && (this.exp.exp === '=' || this.exp.exp === '?')" :placeholder="$t('task.single')">
      <el-option
        v-for="item in this.curmeta.category"
        :label="item.value"
        :key="item.id"
        :value="item">
      </el-option>
    </el-select>
    <el-select class="select" value-key="value" v-else-if="isMultiple()" collapse-tags v-model="exp.val" multiple :placeholder="$t('task.multiple')">
      <el-option
        v-for="item in this.curmeta.category"
        :label="item.value"
        :key="item.id"
        :value="item">
      </el-option>
    </el-select>
    <el-dropdown @command="changeRelation" placement="bottom-end">
      <span class="el-dropdown add">+</span>
      <el-dropdown-menu slot="dropdown">
        <el-dropdown-item :command="'and'">{{$t("task.and")}}</el-dropdown-item>
        <el-dropdown-item :command="'or'">{{$t("task.or")}}</el-dropdown-item>
      </el-dropdown-menu>
    </el-dropdown>
  </div>
</template>
<script>
export default {
  name: 'ep-criteria-exp-item',
  data () {
    return {
      curmeta: {} // 当前对象meta信息
    }
  },
  props: {
    exp: {
      type: Object,
      default: () => {}
    },
    metas: {
      type: Array,
      default: () => []
    }
  },
  created () {
    if (this.exp.name) {
      if (this.exp.name === 'none') {
        this.curmeta = {
          name: 'none',
          type: 'select',
          exp: '?',
          category: ''
        }
      } else {
        for (var meta of this.metas) {
          if (meta.name === this.exp.name) {
            this.curmeta = meta // 得到当前对象的meta信息
            break
          }
        }
      }
    }
  },
  methods: {
    /**
     * label与值的关系符号
     * @author Mr.ma
     * @use 循环遍历上去
     */
    categoryExp () {
      if (this.curmeta.type === 'string') {
        return ['=', 'like']
      } else if (this.curmeta.type === 'number' || this.curmeta.type === 'date') {
        return ['=', '!=', '<', '<=', '>', '>=']
      } else if (this.curmeta.type === 'select') {
        return ['=', 'in', 'notIn']
      }
    },
    /**
     * 是否支持多选值
     * @author Mr.ma
     * @use 判断渲染多选/单选组件
     */
    isMultiple () {
      return this.curmeta.type === 'select' && this.exp.exp !== '=' && this.exp.exp !== '?'
    },
    /**
     * 点击label改变值
     * @param command 当前下标
     * @author Mr.ma
     * @use 改变当前值
     */
    changeType (command) {
      if (command === 'del') {
        this.$emit('del', this.exp)
        return
      }
      this.exp.name = this.metas[command].name
      this.exp.label = this.metas[command].val
      this.curmeta = this.metas[command]
      this.exp.exp = '?'
      this.exp.val = ''
    },
    /**
     * 改变当前值与label的关系
     * @param command 当前下标
     * @author Mr.ma
     * @use 改变当前值
     */
    changeExp (command) {
      let arr = this.categoryExp()
      this.exp.exp = arr[command]
      if (this.curmeta.type === 'select' && this.exp.exp !== '=' && this.exp.exp !== '?') {
        this.exp.val = []
      } else {
        this.exp.val = ''
      }
    },
    /**
     * 点击+号新增关系
     * @param command 当前点击的关系
     * @author Mr.ma
     * @use 告知父元素新增
     */
    changeRelation (command) {
      this.$emit('add', this.exp, (command === 'and' ? 'and' : 'or'))
    }
  }
}
</script>
<style scoped>
.search-box{
  display:inline-block;
  background:#F2F2F2;
  border:1px solid transparent;
  border-radius:4px;
}
.el-dropdown{
  display:inline-block;
  cursor: pointer;
  text-align:center;
  height:26px;
  line-height:26px;
  font-size:12px;
}
.type{
  min-width:50px;
  outline:0;
}
.exp {
  min-width:30px;
  outline:0;
}
.add{
  min-width:30px;
  outline:0;
}
.type-value{
  min-width: 40px;
  outline:0;
}
.border{
  display: block;
  width: 0;
  height: 0;
  border: transparent solid 4px;
  border-top: #717171 solid 4px;
}
.date .el-input__inner{
  border:0;
}
.input-text{
  width:95px;
}
.select{
  height:20px;
  width:180px;
}
.single{
  width:120px;
}
</style>
<style>
.search-box .date{
  width:125px;
}
.search-box .el-input-number--mini{
  width:90px;
}
.search-box .select .el-input__inner{
  height:20px !important;
  line-height:20px;
}
.search-box .select .el-input .el-input__inner{
  border:0;
}
</style>
