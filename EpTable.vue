<template>
  <el-table
    :data="epdata"
    style="width: 100%">
    <el-table-column
      v-for="meta in metas"
      :key="meta.label"
      :label="meta.label"
      :width="meta.width">
      <template slot-scope="scope">
        <!--<div>{{scope.row}}</div>-->
        <div v-if="isType(meta, ['show'])" style="font-size:14px;">{{scope.row[meta.name]}}</div>
        <ep-select v-else-if="isType(meta, ['select'])" :epdata="scope.row" :meta="meta"></ep-select>
        <ep-text v-else-if="isType(meta, ['text'])" :epdata="scope.row" :meta="meta"></ep-text>
      </template>
    </el-table-column>
  </el-table>
</template>
<script>
import epSelect from './EpSelect'
import epText from './EpText'
export default {
  name: 'ep-table',
  props: {
    metas: { // meta信息
      type: Array,
      default: () => []
    },
    epdata: {
      type: Array,
      default: () => []
    }
  },
  components: {
    epSelect, epText
  },
  methods: {
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
  }
}
</script>
