<template>
  <br>
  <el-card class="box-card">
    <template #header>
      <div class="card-header">
        <span>已预约信息</span>
      </div>
    </template>
    <div class="block">
      <p align="left">条件查询：</p>
      <el-date-picker
          v-model="DateRange"
          type="daterange"
          align="left"
          unlink-panels
          range-separator="至"
          start-placeholder="开始日期"
          end-placeholder="结束日期"
          :shortcuts="shortcuts"
          style="width: 80%"
      >
      </el-date-picker>
      <br><br>
      <el-radio-group v-model="status">
      <el-radio-button label="0">未维修</el-radio-button>
      <el-radio-button label="1">未修好</el-radio-button>
      <el-radio-button label="2">已修好</el-radio-button>
      <el-radio-button label="3">所有</el-radio-button>
      </el-radio-group>
      <br><br>
      <el-button type="primary" @click="search()">查询</el-button>
      <el-table
          :data="tableData"
          style="width: 100%"
          :row-class-name="tableRowClassName">
        <el-table-column
            prop="id"
            label="编号"
            width="60">
        </el-table-column>
        <el-table-column
            prop="name"
            label="姓名">
        </el-table-column>
        <el-table-column
            prop="fixdate"
            label="维修时间">
        </el-table-column>
        <el-table-column
            prop="status"
            label="状态"
            width="60"
        >
        </el-table-column>
        <el-table-column prop="status" label="操作">
          <template v-slot="scope">
            <el-button>{{scope.row.id}}</el-button>
          </template>
        </el-table-column>
      </el-table>
    </div>
  </el-card>
</template>

<script>
import axios from "axios";

export default {
  name: "fixmanagement",
  data() {
    return {
      shortcuts: [{
        text: '最近一周',
        value: (() => {
          const end = new Date()
          const start = new Date()
          start.setTime(start.getTime() - 3600 * 1000 * 24 * 7)
          return [start, end]
        })(),
      }, {
        text: '最近一个月',
        value: (() => {
          const end = new Date()
          const start = new Date()
          start.setTime(start.getTime() - 3600 * 1000 * 24 * 30)
          return [start, end]
        })(),
      }, {
        text: '最近三个月',
        value: (() => {
          const end = new Date()
          const start = new Date()
          start.setTime(start.getTime() - 3600 * 1000 * 24 * 90)
          return [start, end]
        })(),
      }],
      DateRange: '',
      status:'',
      tableData:''
    }
  },
  watch: {
    'DateRange': {
      handler: function (val, oldVal) {
        console.log(val, oldVal)
      },
      deep: true
    }
  },
  methods:{
    search() {
      var fixdate1=''
      var fixdate2=''
      if (this.DateRange !== "")
      {
        fixdate1=this.DateRange[0].getFullYear()+"-"+(this.DateRange[0].getMonth()+1)+"-"+this.DateRange[0].getDate()
        fixdate2=this.DateRange[1].getFullYear()+"-"+(this.DateRange[1].getMonth()+1)+"-"+this.DateRange[1].getDate()
      }
      else
      {
        fixdate1="0000-00-00"
        fixdate2="9999-12-31"
      }
      axios.get("/api/GetAllAppointMents",{  //TODO:这里的接口还没有用条件查询的接口
        fixdate1:fixdate1,
        fixdate2:fixdate2,
        status:this.status
          }
      ).then(r => {
        this.tableData=r.data
      }).catch(error => {
        console.log(error)
      })
    },
    tableRowClassName({rowIndex}) {
      // TODO:这个函数还有问题，不能显示
      if (rowIndex === 1) {
        return 'warning-row';
      } else if (rowIndex === 3) {
        return 'success-row';
      }
      return '';
    }
  },
  mounted() {

  }
}
</script>

<style scoped>
.box-card {
  width: 90%;
  margin: 0 auto;
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
 .el-table .warning-row {
   background: oldlace;
 }

.el-table .success-row {
  background: #f0f9eb;
}
</style>