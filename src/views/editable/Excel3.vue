<template>
  <div>
    <el-button size="mini" @click="$refs.editable.insertAt(null, -1)">新增</el-button>
    <el-button size="mini" @click="$refs.editable.clearFilter()">清空筛选条件</el-button>
    <el-button size="mini" @click="$refs.editable.clearSort()">清空排序条件</el-button>
    <el-button size="mini" @click="getAllEvent">获取所有</el-button>
    <el-button size="mini" @click="getUpdateEvent">获取改动</el-button>
    <el-button size="mini" @click="getResultEvent">获取有值数据</el-button>

    <p style="color: red;">渲染成 Excel 表格</p>
    <p style="color: red;">A字段（校验数值）B字段（校验汉字）C字段（校验字母）</p>

    <el-editable ref="editable" class="excel-table2" :data.sync="list" border tooltip-effect="light" size="customSize" style="width: 100%" :editRules="validRules" :editConfig="{trigger: 'dblclick', showIcon: false, showStatus: false}">
      <el-editable-column type="index" align="center" width="50"></el-editable-column>
      <template v-for="(column, index) in columnConfigs">
        <el-editable-column :key="index" v-bind="column" header-align="center" min-width="60" show-overflow-tooltip></el-editable-column>
      </template>
    </el-editable>
  </div>
</template>

<script>
import { MessageBox } from 'element-ui'

export default {
  data () {
    let columns = ['A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I', 'J', 'K', 'L', 'M', 'N', 'O', 'P', 'Q', 'R', 'S', 'T']
    const checkB = (rule, value, callback) => {
      if (value && !/^[\u4e00-\u9fa5]{1,5}$/.test(value)) {
        callback(new Error('请输入1-5个汉字'))
      } else {
        callback()
      }
    }
    const checkC = (rule, value, callback) => {
      if (value && !/^[a-zA-Z]{1,5}$/.test(value)) {
        callback(new Error('请输入1-5个字母'))
      } else {
        callback()
      }
    }
    return {
      list: Array.from(new Array(15), (v, i) => {
        let rest = {}
        columns.forEach(name => {
          rest[name.toLowerCase()] = ''
        })
        return rest
      }),
      columnConfigs: columns.map(name => {
        let column = {
          prop: name.toLowerCase(),
          label: name,
          minWidth: '80',
          sortable: true,
          editRender: {name: 'ElInput'}
        }
        switch (name) {
          case 'A':
            column.filters = [{text: '大于10', value: 10}, {text: '大于50', value: 50}, {text: '大于100', value: 100}]
            column.filterMethod = (value, row, column) => Number(row[column.property] || 0) > value
            break
          case 'C':
            column.filters = [{text: 'a开头', value: 'a'}, {text: 'b开头', value: 'b'}, {text: 'c开头', value: 'c'}]
            column.filterMethod = (value, row, column) => (row[column.property] || '').substring(0, 1) === value
            break
        }
        return column
      }),
      validRules: {
        a: [
          { type: 'number', message: '该列必须输入数字', trigger: 'change' }
        ],
        b: [
          { validator: checkB, trigger: 'blur' }
        ],
        c: [
          { validator: checkC, trigger: 'blur' }
        ]
      }
    }
  },
  methods: {
    getAllEvent () {
      let rest = this.$refs.editable.getRecords()
      MessageBox({ message: JSON.stringify(rest), title: `获取所有数据(${rest.length}条)` })
    },
    getUpdateEvent () {
      let rest = this.$refs.editable.getUpdateRecords()
      MessageBox({ message: JSON.stringify(rest), title: `获取已修改数据(${rest.length}条)` })
    },
    getResultEvent () {
      let rest = this.$refs.editable.getRecords().filter(item => Object.keys(item).some(key => item[key]))
      MessageBox({ message: JSON.stringify(rest), title: `获取有值数据(${rest.length}条)` })
    }
  }
}
</script>

<style>
.excel-table2.el-table--customSize .editable-column {
  height: 30px;
}
.excel-table2 .el-table__body .el-table__row>td {
  cursor: cell;
}
.excel-table2 th,
.excel-table2 .el-table__body .el-table__row>td:first-child,
.excel-table2 .el-table__body .el-table__row:hover>td:first-child {
  background-color: #f5f5f5;
}
.excel-table2 .el-table__body .el-table__row>td:first-child {
  cursor: default;
}
.excel-table2 .el-table__body .el-table__row:hover>td {
  background-color: inherit;
}
.excel-table2 .el-table__body .el-table__row>td.editable-col_checked,
.excel-table2 .el-table__row>td .cell .el-input__inner:focus {
  border: 1px solid #217346;
}
.excel-table2 .el-table__row>td .cell {
  padding: 0;
}
.excel-table2 .el-table__row>td .cell,
.excel-table2 .el-table__row>td .cell .el-input,
.excel-table2 .el-table__row>td .cell .el-input__inner {
  height: 100%;
}
.excel-table2 .el-table__row>td .cell .el-input__inner {
  border-radius: 0;
  padding: 0 2px;
  line-height: 30px;
}
</style>
