<template>
  <div>
    <!-- 新建租赁合同 -->
    <el-dialog
      :title="title"
      :visible.sync="visible"
      :close-on-click-modal="false"
      :show-close="false"
      :rules="rules"
      :close-on-press-escape="false"
      width="1000px"
    >
      <el-form
        ref="addContractData"
        :model="addContractData"
        label-width="90px"
        size="small"
        :rules="rules"
        :inline="true"
        :disabled="type === 'look' ? true : false"
      >
        <h5>基础信息</h5>
        <el-form-item label="合同编号">
          <el-input v-model="addContractData.leaseNo" class="minput" style="width: 220px"></el-input>
          <!-- <el-button type="primary" size="mini" :disabled="type == 'add'?false:true" @click="queryAccountNo()">查重</el-button> -->
        </el-form-item>
        <el-form-item label="签约日期" prop="signTime">
          <el-date-picker v-model="addContractData.signTime" type="date" placeholder="选择日期"></el-date-picker>
        </el-form-item>
        <el-form-item label="签约状态" prop="signType">
          <el-radio-group v-model="addContractData.signType">
            <el-radio :label="1">续签</el-radio>
            <el-radio :label="0">新签</el-radio>
          </el-radio-group>
        </el-form-item>
        <br />

        <el-form-item label="主成交人：">
          <el-input class="minput" v-model="masterDealer.name"></el-input>
          <el-button type="primary" size="mini" @click="showChooseMasterTrader">选择</el-button>
        </el-form-item>

        <el-form-item label="副成交人：">
          <el-input class="minput" v-model="slaveDealer.name"></el-input>
          <el-button type="primary" size="mini" @click="showChooseTrader">选择</el-button>
        </el-form-item>
        <br />
        <el-form-item label="台账编号">
          <el-input :value="ledger.accountNo" class="minput"></el-input>
          <el-button type="primary" size="mini" @click="showChooseLedger">选择</el-button>
        </el-form-item>
        <el-form-item label="成交门店">
          <el-input :value="ledger.storeName" class="minput" :disabled="true"></el-input>
        </el-form-item>
        <br />
        <el-form-item label="合同地址">
          <el-input :value="ledger.address" class="minput" :disabled="true"></el-input>
        </el-form-item>
        <br />
        <el-form-item label="出租类型" prop="rentType">
          <el-radio-group v-model="addContractData.rentType">
            <el-radio :label="1">整租</el-radio>
            <el-radio :label="2">合租</el-radio>
          </el-radio-group>
        </el-form-item>
        <el-form-item label="出租房间">
          <el-select placeholder="请选择" v-model="addContractData.rentHouse">
            <el-option
              v-for="(item, index) in rooms"
              :key="index"
              :label="item.name"
              :value="item.name"
            ></el-option>
          </el-select>
        </el-form-item>
        <br />
        <el-form-item label="合同日期">
          <el-date-picker
            v-model="startDateTime"
            type="date"
            :editable="false"
            :clearable="false"
            @change="changeStartDateTime"
            placeholder="开始日期"
          ></el-date-picker>
          <span>至</span>
          <el-date-picker
            v-model="endDateTime"
            type="date"
            :editable="false"
            :clearable="false"
            @change="changeEndDateTime"
            placeholder="结束日期"
          ></el-date-picker>
        </el-form-item>
        <el-form-item label="合同期限">
          <el-input v-model="addContractData.year" :disabled="true" class="small"></el-input>年
          <el-input v-model="addContractData.month" :disabled="true" class="small"></el-input>月
          <el-input v-model="addContractData.day" :disabled="true" class="small"></el-input>天
        </el-form-item>
        <h5>
          租户信息
          <el-button type="primary" size="mini" @click="showChooseOwner">选择</el-button>
        </h5>
        <el-form-item label="租户编号">
          <el-input :value="owner.memberId" class="minput" :disabled="true"></el-input>
        </el-form-item>
        <el-form-item label="租户姓名">
          <el-input :value="owner.name" class="minput" :disabled="true"></el-input>
        </el-form-item>
        <el-form-item label="身份证号">
          <el-input :value="owner.identity" class="minput" :disabled="true"></el-input>
        </el-form-item>
        <el-form-item v-model="owner.phoneNo" label="手机号码">
          <el-input :value="owner.phoneNo" class="minput" :disabled="true"></el-input>
        </el-form-item>
        <el-form-item v-model="owner.bankName" label="开户银行">
          <el-input :value="owner.bankName" class="minput" :disabled="true"></el-input>
        </el-form-item>
        <el-form-item v-model="owner.bankUserName" label="账户名称">
          <el-input :value="owner.bankUserName" class="minput" :disabled="true"></el-input>
        </el-form-item>
        <el-form-item v-model="owner.bankCardNo" label="银行卡号">
          <el-input :value="owner.bankCardNo" class="minput" :disabled="true"></el-input>
        </el-form-item>
        <h5>协议费用</h5>
        <el-form-item label="租户押金">
          <el-input class="minput" type="number" v-model="addContractData.rentBranch" :disabled="disabled"></el-input>
        </el-form-item>
        <el-form-item label="合同总收">
          <el-input v-model="addContractData.totalIncome" class="minput" :disabled="true"></el-input>
        </el-form-item>
        <el-form-item label="租金总计">
          <el-input v-model="addContractData.totalRent" class="minput" :disabled="true"></el-input>
        </el-form-item>
        <h5>首次费用</h5>
        <el-form-item label="首次费用">
          <el-input class="minput" v-model="addContractData.firstPric" :disabled="true" type="number"></el-input>
        </el-form-item>
        <br>
        <el-form-item label="佣金费">
          <el-input class="minput" v-model="addContractData.rentPric" :disabled="disabled" type="number"></el-input>
        </el-form-item>
        <el-form-item label="卫生费">
          <el-input class="minput" v-model="addContractData.hygienePric" :disabled="disabled" type="number"></el-input>
        </el-form-item>
        <el-form-item label="管理费">
          <el-input class="minput" v-model="addContractData.managePric" :disabled="disabled" type="number"></el-input>
        </el-form-item>
        <el-form-item label="服务费">
          <el-input class="minput" v-model="addContractData.servicePric" :disabled="disabled" type="number"></el-input>
        </el-form-item>
        <el-form-item label="供暖费">
          <el-input class="minput" v-model="addContractData.heatingPric" :disabled="disabled" type="number"></el-input>
        </el-form-item>
        <el-form-item label="其他费用">
          <el-input class="minput" v-model="addContractData.otherPric" :disabled="disabled" type="number"></el-input>
        </el-form-item>
        <br>
        <!--付款协议 paymentAgreement-->
        <h5>付款协议</h5>
        <payment-agreement
          v-if="inited"
          ref="paymentAgreement"
          :list1="paymentAgreements"
          :startTime="startTime"
          :endTime="endTime"
          :type="type"
        ></payment-agreement>

        <!--预收预付 advancePayment-->
        <h5>预收信息</h5>
        <advance-payment :list="advacePayments"></advance-payment>
      </el-form>
      <div slot="footer" class="dialog-footer" style="text-align: center">
        <el-button type="primary" size="mini" @click="handleSubmit">确 定</el-button>
        <el-button size="mini" @click="close">取 消</el-button>
      </div>
    </el-dialog>
    <!-- 新建租赁合同 END -->

    <!-- 选择台账 -->
    <ledger ref="ledger" @sureSelect="sureSelectedLedger"></ledger>
    <!-- 选择台账END -->

    <!-- 主/副成交人 -->
    <trader ref="trader" @choose="chooseTrader"></trader>
    <!-- 主/副成交人 -->

    <!-- 选择业主 -->
    <choose-owner ref="chooseOwner" :showAddOwner="showAddOwner" @sureSelect="sureSelectedOwner"></choose-owner>
    <!-- 选择业主END -->

    <!-- 新建业主 -->
    <owner ref="owner" :sure="handClickOwnerSure" :cancel="handClickOwnerCancel"></owner>
    <!-- 新建业主END -->
  </div>
</template>

<script>
import MTable from "@/components/Table";
import MCascader from "@/components/department/mcascader";
import Ledger from "./ledger";
import Trader from "./trader"; //主副成交人
import Owner from "./owner";
import ChooseOwner from "./chooseOwner";
import PaymentAgreement from "./paymentAgreement";
import AdvancePayment from "./advancePayment";
import {
  getLeaseContractDetail,
  saveLeaseContract,
  updateLeaseAgency,
  getRoom,
  existeLeaseNo
} from "@/api/org-leaseContract";
import EventHub from "@/scripts/eventHub";
import moment from "moment";

export default {
  props: {
    visible: {
      type: Boolean,
      default() {
        return false;
      }
    },
    id: {
      type: Number | String
    },
    leaseNo: {
      type: Number | String
    },
    type: {
      type: String,
      default: "add"
    }
  },
  components: {
    MTable,
    MCascader,
    Ledger,
    Owner,
    ChooseOwner,
    PaymentAgreement,
    AdvancePayment,
    Trader
  },
  data() {
    return {
      owner: {}, //业主信息选择填充
      ledger: {}, //台账编号选择填充
      startTime: moment(Date.now()).format("YYYY-MM-DD"),
      endTime: moment(Date.now()).format("YYYY-MM-DD"),
      startDateTime: Date.now(),
      endDateTime: Date.now(),
      oldStartDateTime: Date.now(),
      oldEndDateTime: Date.now(),
      pageSize: 10,
      accountNo: "",
      leaseSysNo: "",
      addContractData: {
        signTime: "",
        signType: "",
        time: [],
        year: 0,
        month: 0,
        day: 0,
        leaseNo: '',
        rentBranch: 0,
        firstPric: 0,// 首次费用
        rentPric: 0,//佣金
        hygienePric: 0,//卫生费
        managePric: 0,//管理费
        servicePric: 0,//服务费
        heatingPric: 0,//供暖费
        otherPric: 0,//其他费用
        rentType: "", //出租类型
        totalIncome: "", //合同总收
        totalRent: "", //租金总计
        rentHouse: "" // 房间
      },
      activeCjr: "masterDealer", // 当前待选择成交人， masterDealer：主成交人， slaveDealer: 副成交人
      masterDealer: {
        name: ""
      }, // 选择的主成交人
      slaveDealer: {
        name: ""
      }, // 选择的副成交人
      cacheAdvacePayment: {},
      advacePayments: [],
      paymentAgreements: [],
      rooms: [],
      rules: {
        signTime: [
          { required: true, message: "请选择签约日期", trigger: "change" }
        ],
        signType: [
          { required: true, message: "请选择签约状态", trigger: "change" }
        ],
        rentType: [
          { required: true, message: "请选择出租类型", trigger: "change" }
        ],
        time: [{ required: true, message: "请选择合同日期", trigger: "change" }]
      },
      inited: false
    };
  },
  computed: {
    title() {
      return this.type === "add" ? "新建租赁合同" : this.type === "look" ? "查看租赁合同" : "编辑租赁合同";
    },
    disabled() {
      // return this.type === "add" ? false : true;
      return this.type === false;
    }
  },
  created() {
    if (this.type === "edit" || this.type === "look") {
      this.getData();
    } else {
      this.inited = true;
    }
    EventHub.$on("advacePaymentData", res => {
      console.log("advacePaymentData=====", res);
      this.cacheAdvacePayment[res.index] = res.data;
      let arr = [];
      Object.keys(this.cacheAdvacePayment).forEach(
        k => (arr = arr.concat(this.cacheAdvacePayment[k]))
      );
      this.advacePayments = arr;
      this.addContractData.totalRent = res.rentTotal
      this.addContractData.totalIncome = res.rentTotal * 1 + this.addContractData.rentBranch * 1
    });
  },
  watch: {
    'addContractData.rentBranch'(val) {
      this.addContractData.totalIncome = this.addContractData.totalRent * 1 + val * 1 || 0
    },
    'addContractData.rentPric'(val) {
      this.addContractData.firstPric = this.addContractData.hygienePric * 1 + this.addContractData.managePric * 1 + this.addContractData.otherPric * 1 + this.addContractData.heatingPric * 1 + this.addContractData.servicePric * 1 + val * 1 || 0
    },
    'addContractData.hygienePric'(val) {
      this.addContractData.firstPric = this.addContractData.rentPric * 1 + this.addContractData.managePric * 1 + this.addContractData.otherPric * 1 + this.addContractData.heatingPric * 1 + this.addContractData.servicePric * 1 + val * 1 || 0
    },
    'addContractData.managePric'(val) {
      this.addContractData.firstPric = this.addContractData.rentPric * 1 + this.addContractData.hygienePric * 1 + this.addContractData.otherPric * 1 + this.addContractData.heatingPric * 1 + this.addContractData.servicePric * 1 + val * 1 || 0
    },
    'addContractData.servicePric'(val) {
      this.addContractData.firstPric = this.addContractData.rentPric * 1 + this.addContractData.hygienePric * 1 + this.addContractData.otherPric * 1 + this.addContractData.heatingPric * 1 + this.addContractData.managePric * 1 + val * 1 || 0
    },
    'addContractData.heatingPric'(val) {
      this.addContractData.firstPric = this.addContractData.rentPric * 1 + this.addContractData.hygienePric * 1 + this.addContractData.otherPric * 1 + this.addContractData.servicePric * 1 + this.addContractData.managePric * 1 + val * 1 || 0
    },
    'addContractData.otherPric'(val) {
      this.addContractData.firstPric = this.addContractData.rentPric * 1 + this.addContractData.hygienePric * 1 + this.addContractData.heatingPric * 1 + this.addContractData.servicePric * 1 + this.addContractData.managePric * 1 + val * 1 || 0
    }
  },
  methods: {
    getData() {
      getLeaseContractDetail(this.id)
        .then(res => {
          // 系统编号
          this.leaseSysNo = res.body.leaseSysNo
          // 合同编号
          this.addContractData.leaseNo = res.body.leaseNo
          // 签约日期 签约状态
          this.addContractData.signTime = res.body.signTime;
          this.addContractData.signType = res.body.signType;
          this.addContractData.totalIncome = res.body.totalIncome || 0;
          this.addContractData.totalRent = res.body.totalRent || 0;
          this.masterDealer.name = res.body.masterDealer;
          this.slaveDealer.name = res.body.slaveDealer;
          this.addContractData.rentType = res.body.rentType;
          this.addContractData.rentHouse = res.body.rentHouse;
          // 台账信息
          this.ledger.accountNo = res.body.accountNo;
          this.ledger.masterBroker = res.body.masterBroker;
          this.ledger.storeName = res.body.storeName;
          this.ledger.address = res.body.address;
          this.ledger.companyId = res.body.companyId; //页面没有但需要传的字段
          this.ledger.storeId = res.body.storeId; //页面没有但需要传的字段

          // 合同日期
          this.startDateTime = this.oldEndDateTime = new Date(
            res.body.startTime
          );
          this.endDateTime = this.oldEndDateTime = new Date(res.body.endTime);

          this.addContractData.year = res.body.year;
          this.addContractData.month = res.body.month;
          this.addContractData.day = res.body.day;
          this.startTime = res.body.startTime;
          this.endTime = res.body.endTime;
          // 业主信息
          this.owner.memberId = res.body.memberInfo.memberId;
          this.owner.name = res.body.memberInfo.name; //页面没有但需要传的字段
          this.owner.identity = res.body.memberInfo.identity;
          this.owner.phoneNo = res.body.memberInfo.phoneNo;
          this.owner.bankName = res.body.memberInfo.bankName;
          this.owner.bankUserName = res.body.memberInfo.bankUserName;
          this.owner.bankCardNo = res.body.memberInfo.bankCardNo;
          this.addContractData.rentBranch = res.body.rentBranch;
          // 首次费用
          this.addContractData.firstPric = res.body.firstPric || 0;
          this.addContractData.rentPric = res.body.rentPric || 0;
          this.addContractData.hygienePric = res.body.hygienePric || 0;
          this.addContractData.managePric = res.body.managePric || 0;
          this.addContractData.servicePric = res.body.servicePric || 0;
          this.addContractData.heatingPric = res.body.heatingPric || 0;
          this.addContractData.otherPric = res.body.otherPric || 0;
          // 付款协议
          this.paymentAgreements = res.body.leasePaymentAgreementVOS;
          // 预收预付
          this.advacePayments = res.body.leaseCostDetailVOS;
          this.cacheAdvacePayment[0] = res.body.leaseCostDetailVOS
          this.info = res.body;
          this.inited = true;
        })
        .catch(() => {
          this.inited = true;
        });
    },
    handClickOwnerSure() {
      this.$refs.owner.close();
    },
    handClickOwnerCancel() {
      this.$refs.owner.close();
    },
    showAddOwner() {
      this.$refs.owner.show();
    },
    showChooseOwner() {
      this.$refs.chooseOwner.show();
    },
    showChooseLedger() {
      this.$refs.ledger.show();
    },
    // 选择台账
    sureSelectedLedger(ledger) {
      this.ledger = ledger;
      this.getRoom(ledger.accountNo);
    },
    getRoom(accountNo) {
      getRoom({ accountNo }).then(res => {
        this.rooms = res.body;
      });
    },
    //选择副成交人
    showChooseTrader() {
      this.activeCjr = "slaveDealer";
      this.$refs.trader.show();
    },
    //选择主成交人
    showChooseMasterTrader() {
      this.activeCjr = "masterDealer";
      this.$refs.trader.show();
    },
    // 选择主/副成交人
    chooseTrader(data = {}) {
      // console.log("chooseTrader=", data);
      this[this.activeCjr] = data;
    },
    // chooseOwner选择业主
    sureSelectedOwner(owner) {
      // console.log("sureSelectedOwner.....", owner);
      this.owner = owner;
    },
    changeContractData(startTime, endTime) {
      // 清空预付信息
      this.advacePayments = []
      // 合同期限
      this.startTime = startTime;
      this.endTime = endTime;
      let { year, month, day } = this.monthDayDiff(startTime, endTime)
      if (day >= 30) {
        if (day == 31) {
          this.addContractData.day = "1";
        }else {
          this.addContractData.day = "0";
        }
        month++
      }else {
        this.addContractData.day = (day == 29 || month < 0) ? day + 1 : day + 1;
      }
      if (month == 12) {
        this.addContractData.month = "0";
        year++
      }else if (month < 0){
        this.addContractData.month = month + 12;
        year--
      }else {
        this.addContractData.month = month;
      }
      this.addContractData.year = year;
    },
    monthDayDiff(startTime,endTime) {
      let flag = [1, 3, 5, 7, 8, 10, 12, 4, 6, 9, 11, 2];
      let start = new Date(startTime)
      let end = new Date(endTime)
      let year = end.getFullYear() - start.getFullYear()
      let month = end.getMonth() - start.getMonth()
      let day = end.getDate() - start.getDate()
      if (month < 0) {
        year--
        month = end.getMonth() + (12 - start.getMonth())
      }
      if (day < 0) {
        month--
        let index = flag.findIndex((temp) => {
            return temp === start.getMonth() + 1
        });
        let monthLength;
        if (index <= 6) {
            monthLength = 31;
        } else if (index > 6 && index <= 10) {
            monthLength = 30;
        } else {
            monthLength = 31;
        }
        day = end.getDate() + (monthLength - start.getDate());
      }
      return {year:year,month:month,day:day}
    },
    handleSubmit() {
      this.$refs.paymentAgreement.payments.forEach((d, i) => {
        d['leaseCostDetailRequests'] = this.cacheAdvacePayment[i]
      })
      const form = {
        ...this.addContractData,
        ...this.owner,
        ...this.ledger,
        leasePaymentAgreementRequests: this.$refs.paymentAgreement.payments
      };
      form.signTime = moment(this.addContractData.signTime).format(
        "YYYY-MM-DD"
      );
      form.startTime = this.startTime;
      form.endTime = this.endTime;
      form.memberName = this.owner.name;
      form.companyId = this.ledger.companyId;
      form.storeId = this.ledger.storeId;
      form.masterDealer = this.masterDealer.name + ""; // 主成交人
      form.slaveDealer = this.slaveDealer.name + ""; // 副成交人
      form.rentBranch= Number(form.rentBranch)
      form.firstPric = Number(this.addContractData.firstPric)
      form.rentPric = Number(this.addContractData.rentPric)
      form.hygienePric = Number(this.addContractData.hygienePric)
      form.managePric = Number(this.addContractData.managePric)
      form.servicePric = Number(this.addContractData.servicePric)
      form.heatingPric = Number(this.addContractData.heatingPric)
      form.otherPric = Number(this.addContractData.otherPric)
      form.signType = this.addContractData.signType
      form.rentType = this.addContractData.rentType
      // console.log("handleSubmit form=", form);    
      // 此处需要判断字段不能为空
      if (this.addContractData.leaseNo === '' || this.addContractData.leaseNo === undefined) {
        this.$message({
          type: "warning",
          message: "合同编号不能为空"
        });
        return;
      }
      if (form.signTime === 'Invalid date' || form.signTime === '') {
        this.$message({
          type: "warning",
          message: "请选择签约日期!"
        });
        return;
      }
      if (!Number.isInteger(form.signType)) {
        this.$message({
          type: "warning",
          message: "请选择签约状态!"
        });
        return;
      }
      if (!form.accountNo) {
        this.$message({
          type: "warning",
          message: "请选择台账编号!"
        });
        return;
      }
      if (form.rentType === "") {
        this.$message({
          type: "warning",
          message: "请选择出租类型"
        });
        return;
      }
      if (form.memberId === undefined || form.memberId === "") {
        this.$message({
          type: "warning",
          message: "请选择租户信息"
        });
        return;
      }
      // if (form.rentBranch === 0 || form.rentBranch === undefined) {
      //   this.$message({
      //     type: "warning",
      //     message: "请输入租户租金"
      //   });
      //   return;
      // }
      // 租赁合同新增或修改
      if (this.type === "add") {
        saveLeaseContract(form)
          .then(res => {
            // console.log("handleSubmit res=", res);
            if (res.code == 1000) {
              this.close();
              this.$emit("update");
              this.$message({
                message: "新增成功",
                type: "success"
              });
            } else {
              this.$message({
                message: "新增失败",
                type: "error"
              });
            }
          })
          .catch(err => {
            console.error(err);
            if (err.code) {
              this.$message({
                message: `接口异常，code：【${err.code}】`,
                type: "error"
              });
            }
          });
      } else if (this.type === "edit"){
        form.leaseId = this.id;
        form.leaseNo = this.addContractData.leaseNo;
        form.leaseSysNo = this.leaseSysNo
        form.leasePaymentAgreementRequests.forEach((d, i) => {
          if (this.cacheAdvacePayment[i] != undefined) {
            const index = this.cacheAdvacePayment[i].findIndex(d => d.classification == '押金')
            if (index >= 0) {
              this.cacheAdvacePayment[i].splice(index, 1)
              d.leaseCostDetailRequests = this.cacheAdvacePayment[i]
            }
          }
        })
        updateLeaseAgency(form)
          .then(res => {
            // console.log("handleSubmit res=", res);
            if (res.code == 1000) {
              this.close();
              this.$emit("update");
              this.$message({
                message: "修改成功",
                type: "success"
              });
            } else {
              this.$message({
                message: "修改失败",
                type: "error"
              });
            }
          })
          .catch(err => {
            console.error(err);
          });
      }else {
        this.close();
      }
    },
    close() {
      this.$emit("close");
    },
    /**
     * 合同编号查重复
    */
    queryAccountNo() {
      existeLeaseNo(this.addContractData.leaseNo).then(res => {
        let { code, body } = res
        if (code == 1000) {
          if (body == true) {
            this.$message({
              type: "warning",
              message: "合同编号重复！请重新输入"
            });
          }else {
            this.$message({
              type: "success",
              message: "合同编号可用！"
            });
          }
        }
      })
    },
    changeStartDateTime(date) {
      // console.log("changeStartDateTime=", date);
      if (date !== null) {
        const startTime = moment(date).format("YYYY-MM-DD");
        if (startTime && this.endTime) {
          if (startTime > this.endTime) {
            this.$message({
              message: "开始时间不能大于结束时间",
              type: "warning"
            });
            this.startDateTime = this.oldStartDateTime;
            return;
          }
          if (startTime == this.startTime) {
            // console.log('开始时间未变化')
            return
          }
          this.oldStartDateTime = this.startDateTime;
          this.startTime = startTime;
          this.changeContractData(startTime, this.endTime);
        }
      }
    },
    changeEndDateTime(date) {
      // console.log("changeEndDateTime=", date);
      if (date !== null) {
        const endTime = moment(date).format("YYYY-MM-DD");
        if (this.startTime && endTime) {
          if (this.startTime > endTime) {
            this.$message({
              message: "开始时间不能大于结束时间",
              type: "warning"
            });
            this.endDateTime = this.oldEndDateTime;
            return;
          }
          if (endTime == this.endTime) {
            // console.log('结束时间未变化')
            return
          }
          this.oldEndDateTime = this.endDateTime;
          this.endTime = endTime;
          this.changeContractData(this.startTime, endTime);
        }
      }
    }
  }
};
</script>

<style scoped>
.small {
  width: 60px;
}
h5 {
  font-size: 16px;
  margin-bottom: 10px;
}
</style>
