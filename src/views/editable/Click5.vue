<template>
  <div v-loading="loading">
    <el-button type="success" size="mini" @click="$refs.editable.insert({flag3: false})">新增一行</el-button>
    <el-button type="success" size="mini"  @click="$refs.editable.insertAt({flag3: false}, -1)">在最后新增一行</el-button>
    <el-button type="danger" size="mini" @click="$refs.editable.removeSelecteds()">删除选中</el-button>
    <el-button type="info" size="mini" @click="$refs.editable.revert()">放弃更改</el-button>
    <el-button type="info" size="mini" @click="$refs.editable.clear()">清空数据</el-button>
    <el-button type="warning" size="mini" @click="submitEvent">保存</el-button>
    <el-button type="primary" size="mini" @click="getInsertEvent">获取新增数据</el-button>
    <el-button type="primary" size="mini" @click="getUpdateEvent">获取已修改数据</el-button>
    <el-button type="primary" size="mini" @click="getRemoveEvent">获取已删除数据</el-button>
    <el-button type="primary" size="mini" @click="getAllEvent">获取所有数据</el-button>

    <p style="color: red;">动态列渲染</p>

    <el-editable ref="editable" stripe border show-summary size="medium" style="width: 100%">
      <el-editable-column type="index" width="55"></el-editable-column>
      <el-editable-column v-for="(item, index) in columnConfigs" :key="index" v-bind="item"></el-editable-column>
      <el-editable-column label="操作">
        <template slot-scope="scope">
          <el-popover placement="top" width="160" v-model="scope.row.flag3">
            <p>这是一段内容这是一段内容确定删除吗？</p>
            <div style="text-align: right; margin: 0">
              <el-button type="text" size="mini" @click="scope.row.flag3 = false">取消</el-button>
              <el-button type="primary" size="mini" @click="removeEvent(scope.row)">确定</el-button>
            </div>
            <el-button slot="reference" size="mini" type="danger">删除</el-button>
          </el-popover>
        </template>
      </el-editable-column>
    </el-editable>
  </div>
</template>

<script>
import XEUtils from 'xe-utils'
import { MessageBox } from 'element-ui'
import listData from '@/common/json/editable/list.json'
import regionData from '@/common/json/editable/region.json'
import sexData from '@/common/json/editable/sex.json'
import columnsData from '@/common/json/editable/columns.json'

export default {
  data () {
    return {
      loading: false,
      columnConfigs: [],
      sexList: [],
      regionList: []
    }
  },
  created () {
    this.init()
  },
  methods: {
    init () {
      let sexPromise = this.getSexJSON()
      let regionPromise = this.getRegionJSON()
      this.findList()
      this.getColumnConfigs().then(data => {
        let sexItem = data.find(column => column.prop === 'sex')
        sexItem.editRender.options = []
        sexPromise.then(rest => {
          sexItem.editRender.options = rest
        })
        let regionItem = data.find(column => column.prop === 'region')
        regionItem.editRender.attrs = {options: []}
        regionPromise.then(rest => {
          regionItem.editRender.attrs.options = rest
        })
        let birthdateItem = data.find(column => column.prop === 'birthdate')
        birthdateItem.editRender.attrs = {
          type: 'date',
          format: 'yyyy-MM-dd'
        }
        let rateItem = data.find(column => column.prop === 'rate')
        rateItem.editRender.type = 'visible'

        this.columnConfigs = data
      })
    },
    findList () {
      this.loading = true
      this.getDataJSON().then(data => {
        this.$refs.editable.reload(data)
        this.loading = false
      }).catch(e => {
        this.loading = false
      })
    },
    removeEvent (row) {
      row.flag3 = false
      this.$refs.editable.remove(row)
    },
    submitEvent () {
      let { insertRecords, removeRecords, updateRecords } = this.$refs.editable.getAllRecords()
      this.postJSON('url', { insertRecords, removeRecords, updateRecords }).then(data => {
        this.findList()
      })
    },
    getInsertEvent () {
      let rest = this.$refs.editable.getInsertRecords()
      MessageBox({ message: JSON.stringify(rest), title: `获取新增数据(${rest.length}条)` })
    },
    getUpdateEvent () {
      let rest = this.$refs.editable.getUpdateRecords()
      MessageBox({ message: JSON.stringify(rest), title: `获取已修改数据(${rest.length}条)` })
    },
    getRemoveEvent () {
      let rest = this.$refs.editable.getRemoveRecords()
      MessageBox({ message: JSON.stringify(rest), title: `获取已删除数据(${rest.length}条)` })
    },
    getAllEvent () {
      let rest = this.$refs.editable.getRecords()
      MessageBox({ message: JSON.stringify(rest), title: `获取所有数据(${rest.length}条)` })
    },
    postJSON (data) {
      return new Promise(resolve => {
        setTimeout(() => {
          resolve('保存成功')
        }, 300)
      })
    },
    getSexJSON () {
      return new Promise(resolve => {
        setTimeout(() => resolve(XEUtils.clone(sexData, true)), 100)
      })
    },
    getColumnConfigs () {
      return new Promise(resolve => {
        setTimeout(() => resolve(XEUtils.clone(columnsData, true)), 100)
      })
    },
    getDataJSON () {
      return new Promise(resolve => {
        setTimeout(() => resolve(XEUtils.clone(listData, true)), 350)
      })
    },
    getRegionJSON () {
      return new Promise(resolve => {
        setTimeout(() => resolve(XEUtils.clone(regionData, true)), 200)
      })
    }
  }
}
</script>
