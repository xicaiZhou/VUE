<!-- 考勤报表 -->
<template>
  <div class="mtools">
    <el-form label-width="100px" size="small" :inline="true">
      <m-cascader :companyId.sync="table.parms.companyId" :storeId.sync="table.parms.storeId"></m-cascader>
      <el-form-item label="在职状态">
        <el-select placeholder="请选择">
          <el-option v-for="item in createUsers" :key="item.id" :label="item.name" :value="item.id"> </el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="员工状态">
        <el-select placeholder="请选择">
          <el-option v-for="item in createUsers" :key="item.id" :label="item.name" :value="item.id"> </el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="姓名">
        <el-select placeholder="请选择" v-model="table.parms.name">
          <el-option v-for="item in createUsers" :key="item.id" :label="item.name" :value="item.id"> </el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="员工ID">
        <el-input class="minput" placeholder="请输入员工ID" v-model="table.parms.userId"></el-input>
      </el-form-item>
      <el-form-item label="考勤日期">
        <el-date-picker
          v-model="table.parms.beginDate"
          type="date"
          placeholder="选择开始日期"
          format="yyyy-MM-dd"
          value-format="yyyy-MM-dd"
          :picker-options="pickerOptionsStart">
        </el-date-picker>
        至
        <el-date-picker
          v-model="table.parms.endDate"
          type="date"
          placeholder="选择结束时间"
          format="yyyy-MM-dd"
          value-format="yyyy-MM-dd"
          :picker-options="pickerOptionsEnd">
        </el-date-picker>
      </el-form-item>
      <el-button type="primary" size="mini" @click="currentChange(1)">查询</el-button>
    </el-form>
    <m-table
      :hasPaginationSizes="true"
      :data="table.list"
      :setting="table.setting"
      :loading="table.loading"
      :total="table.total"
      :pageNum="table.page"
      :pageSize="table.rows"
      @currentChange="currentChange"
      @handleSizeChange="handleSizeChange"
    >
      <el-table-column label="操作" width="70px" fixed="right">
        <template slot-scope="scope">
          <el-button type="text" @click="editAttendance(scope.row)">编辑</el-button>
        </template>
      </el-table-column>
    </m-table>
    
    <!-- 编辑考勤报表 -->
    <el-dialog
      title="编辑考勤报表"
      :visible.sync="dialogVisible2"
      :close-on-click-modal="false"
      :show-close="false"
      :close-on-press-escape="false"
      width="350px">
      <el-form label-width="70px" size="small">
        <el-form-item label="阶段">
          <el-select placeholder="请选择">
            <el-option v-for="item in createUsers" :key="item.id" :label="item.name" :value="item.id"> </el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="状态">
          <el-select placeholder="请选择">
            <el-option v-for="item in createUsers" :key="item.id" :label="item.name" :value="item.id"> </el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="时长">
          <el-select placeholder="请选择">
            <el-option v-for="item in createUsers" :key="item.id" :label="item.name" :value="item.id"> </el-option>
          </el-select>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer" style="text-align: center">
        <el-button size="mini" type="primary" @click="submitEditAttendance()">确 认</el-button>
        <el-button size="mini" @click="dialogVisible2=false">取 消</el-button>
      </div>
    </el-dialog>
    <!-- 编辑考勤报表END -->


  </div>
</template>

<script>
import MTable from "@/components/Table";
import moment from 'moment'
import MCascader from "@/components/department/mcascader";
import { FindByName } from "@/api/org-user";

export default {
  components: {
    MTable,
    MCascader
  },
  data() {
    return {
      pickerOptionsStart: {
        disabledDate: time => {
          let endDateVal = this.table.parms.endDate;
          if (endDateVal) {
            return time.getTime() > new Date(endDateVal).getTime();
          }
        }
      },
      pickerOptionsEnd: {
        disabledDate: time => {
          let beginDateVal = this.table.parms.beginDate;
          if (beginDateVal) {
            return (
              time.getTime() < new Date(beginDateVal).getTime() - 1 * 24 * 60 * 60 * 1000
            )
          }
        }
      },
      createUsers: [{ name: "所有项", id: "" }],
      table: {
        loading: false,
        list: [{}],
        setting: [
          {
            label: "公司",
            value: "",
            formatter(row, column, cellValue, index) {
              return '北京'
            }
          }, {
            label: "部门",
            value: "",
            formatter(row, column, cellValue, index) {
              return ''
            }
          }, {
            label: "姓名",
            value: "",
            formatter(row, column, cellValue, index) {
              return ''
            }
          }, {
            label: "上午",
            value: "",
            formatter(row, column, cellValue, index) {
              return ''
            }
          }, {
            label: "下午",
            value: "",
            formatter(row, column, cellValue, index) {
              return ''
            }
          }, {
            label: "岗位",
            value: "",
            formatter(row, column, cellValue, index) {
              return ''
            }
          }, {
            label: "日期",
            value: "",
            width: 120,
            formatter(row, column, cellValue, index) {
              return ''
            }
          }, {
            label: "员工ID",
            value: "",
            formatter(row, column, cellValue, index) {
              return ''
            }
          }
        ],
        parms: {
          companyId: "",
          storeId: "",
          beginDate: "",
          endDate: "",
          name: "",
          userId: ""
        },
        total: 10,
        page: 1,
        rows: 10
      },
      dialogVisible2: false
    };
  },
  computed: {},
  watch: {
    "table.parms.beginDate"(val) {
      if (val) {
        this.table.parms.beginDate = moment(val).format("YYYY-MM-DD");
      }else {
        this.table.parms.beginDate = ""
      }
    },
    "table.parms.endDate"(val) {
      if (val) {
        this.table.parms.endDate = moment(val).format("YYYY-MM-DD");
      }else {
        this.table.parms.endDate = ""
      }
    },
  },
  methods: {
    // 设置显示页数
    handleSizeChange(val) {
      this.currentChange(1, val)
    },
		/**
     * 获取分页数据
    */
    currentChange(val, totalNum) {
      if (val || val === 0) {
        this.table.page = val;
      }
      if (totalNum || totalNum === 0) {
        this.table.rows = totalNum;
      }
      let { page, rows, parms } = this.table;
      // getTempAccountByPage({ ...parms, page, rows }).then(res => {
			// 	let { code, body } = res;
      //   if (code == 1000) {
      //     this.table.list = body.rows
      //     this.table.total = body.total;
      //   }
			// });
    },
    /**
     * 编辑
    */
    editAttendance(val) {
      this.dialogVisible2 = true
    },
    /**
     * 确认按钮
    */
    submitEditAttendance() {
      this.dialogVisible2 = false
    }
  },
  created() {},
  mounted() {}
};
</script>
<style lang='scss' scoped>
h5 {
  font-size: 16px;
  margin-bottom: 10px;
}
</style>