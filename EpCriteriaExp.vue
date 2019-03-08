<template>
  <div style="display:inline-block;line-height:20px;margin:2px">
    <ep-criteria-exp-item
      v-if="isSingle"
      :exp="exp.items[0]"
      :metas="metas"
      v-on:add="addData"
    ></ep-criteria-exp-item>
    <div v-if="!isSingle"
      style="padding: 2px;vertical-align: top;line-height:20px;"
      :style="{'background-color' : colorval}">
      <template v-for="(expItem,index) in exp.items">
        <ep-criteria-exp-item
          v-if="isItem(expItem)"
          :key="expItem.id"
          :exp="expItem"
          :metas="metas"
          v-on:del="updateData"
          v-on:add="addData"
        >
        </ep-criteria-exp-item>
        <ep-criteria-exp v-if="!isItem(expItem)"
          :key="expItem.id"
          :isChildren="true"
          :exp="expItem"
          :metas="metas"
          v-on:resetData="resetData"
        >
        </ep-criteria-exp>
        <button v-if="index < (exp.items.length - 1)" :key="index" @click="switchRelation()" type="button" style="border:0;background-color:rgba(245, 245, 220, 0);">
          {{$t(exp.relation === 'or' ? '或' : '且')}}
        </button>
      </template>
    </div>
  </div>
</template>
<script>
import epCriteriaExpItem from './EpCriteriaExpItem'
export default {
  name: 'ep-criteria-exp',
  props: {
    exp: { // 当前对象
      type: Object,
      default: () => {}
    },
    metas: { // meta信息
      type: Array,
      default: () => []
    },
    isChildren: { // 是否存在子关系
      type: Boolean,
      default: false
    }
  },
  components: {
    epCriteriaExpItem
  },
  computed: {
    /**
     * 判断当前是的关系是怎么处境，决定不同组件
     * @author Mr.ma
     */
    isSingle () {
      return this.exp.items && this.exp.items.length === 1 && this.exp.items[0].name && !this.isChildren
    },
    /**
     * 生成随机背景色
     * @author Mr.ma
     * @use 直观看到当前关系的处境
     */
    colorval () {
      return '#' + Math.round(Math.random() * 100 + 150).toString(16) +
        Math.round(Math.random() * 100 + 150).toString(16) +
        Math.round(Math.random() * 100 + 150).toString(16)
    }
  },
  methods: {
    isItem (expItem) {
      return expItem.name && !expItem.items
    },
    addData (targetItem, type) {
      if (this.exp.items && this.exp.items.length === 1 && this.exp.items[0].name && !this.isChildren) {
        this.exp.relation = type
      }
      var f = type === this.exp.relation
      for (var i = 0; i < this.exp.items.length; i++) {
        if (this.exp.items[i] === targetItem) {
          if (f) {
            this.exp.items.splice(i, 0, {name: 'none', label: '-请选择-', exp: '?', val: '', id: Math.random()})
          } else {
            var newGroup = {
              relation: type,
              items: [targetItem, {name: 'none', label: '-请选择-', exp: '?', val: '', id: Math.random()}]
            }
            this.exp.items.splice(i, 1, newGroup)
          }
          break
        }
      }
    },
    /**
     * 改变关系
     * @author Mr.ma
     * @use or / and
     */
    switchRelation () {
      this.exp.relation = this.exp.relation === 'or' ? 'and' : 'or'
    },
    /**
     * 删除数据之后的更新
     * @param 当前删除的对象
     * @author Mr.ma
     */
    updateData (exp) {
      // console.log('before = ' + JSON.stringify(this.exp))
      for (var i = 0; i < this.exp.items.length; i++) {
        if (this.exp.items[i] === exp) {
          this.exp.items.splice(i, 1)
          break
        }
      }
      if (this.exp.items.length === 0) {
        this.$emit('del', this.exp)
      }
      // console.log('end = ' + JSON.stringify(this.exp))
      if (this.exp.items.length === 1 && this.exp.items[0].items) {
        // console.log('子集只有1项，且该项为集合，则退出当前组提升到外部')
        this.exp.items = this.exp.items[0].items
      } else if (this.exp.items.length === 1 && !this.exp.items[0].items) {
        // console.log('子集只有1项，且该项为内容，则退出当前组提升到外部')
        this.$emit('resetData', this.exp)
      }
    },
    /**
     * 剩余一项后的操作
     * @param 当前对象
     * @author Mr.ma
     */
    resetData (exp) {
      var t = exp.items[0]
      for (var i = 0; i < this.exp.items.length; i++) {
        if (this.exp.items[i] === exp) {
          this.exp.items.splice(i, 1, t)
          break
        }
      }
    }
  }
}
</script>
