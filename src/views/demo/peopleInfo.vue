<template>
  <div>
    <!-- 信息 -->
    <div style="height:30px;width:100%">
      <el-button @click="$router.back()" style="float:right">返回</el-button>
    </div>

    <div class="info-img">
      <div class="img-box">
        <img :src="photo" alt="一寸照" style="width:200px" />
      </div>
      <div class="img-box1">
        <div class="info-form">
          <div class="info-item">
            <div class="title-form">
              <span style="float:right;margin-top:7%">姓名：</span>
            </div>
            <div class="content-form">
              <span class="font-content">{{this.pname}}</span>
            </div>
          </div>
          <div class="info-item">
            <div class="title-form">
              <span style="float:right;margin-top:7%">姓别：</span>
            </div>
            <div class="content-form">
              <span class="font-content">{{this.pgender}}</span>
            </div>
          </div>
          <div class="info-item">
            <div class="title-form">
              <span style="float:right;margin-top:7%">手机号：</span>
            </div>
            <div class="content-form">
              <span class="font-content">{{this.phoneNumber}}</span>
            </div>
          </div>
          <div class="info-item">
            <div class="title-form">
              <span style="float:right;margin-top:7%">职业技能培训：</span>
            </div>
            <div class="content-form">
              <span class="font-content">{{this.trainingCategory}}</span>
            </div>
          </div>
          <div class="info-item">
            <div class="title-form">
              <span style="float:right;margin-top:7%">培训等级：</span>
            </div>
            <div class="content-form">
              <span class="font-content">{{this.trainingLevel}}</span>
            </div>
          </div>
          <div class="info-item">
            <div class="title-form">
              <span style="float:right;margin-top:7%">审核状态：</span>
            </div>
            <div class="content-form">
              <span class="font-content">{{this.personneStatus}}</span>
            </div>
          </div>
          <div class="info-item">
            <div class="title-form">
              <span style="float:right;margin-top:7%">分班：</span>
            </div>
            <div class="content-form">
              <span class="font-content">{{pclass||"未分班"}}</span>
            </div>
          </div>
        </div>
        <div class="info-form1">
          <div class="info-item">
            <div class="title-form">
              <span style="float:right;margin-top:7%">身份证号：</span>
            </div>
            <div class="content-form">
              <span class="font-content">{{this.idNumber}}</span>
            </div>
          </div>
          <div class="info-item">
            <div class="title-form">
              <span style="float:right;margin-top:7%">人员类别：</span>
            </div>
            <div class="content-form">
              <span class="font-content">{{this.personnelCategory}}</span>
            </div>
          </div>
          <div class="info-item">
            <div class="title-form">
              <span style="float:right;margin-top:7%">报名时间：</span>
            </div>
            <div class="content-form">
              <span class="font-content">{{this.creationTime}}</span>
            </div>
          </div>
          <div class="info-item">
            <div class="title-form">
              <span style="float:right;margin-top:7%">培训职业（工种）：</span>
            </div>
            <div class="content-form">
              <span class="font-content">{{this.trainingType}}</span>
            </div>
          </div>
          <div class="info-item">
            <div class="title-form">
              <span style="float:right;margin-top:7%">微信号：</span>
            </div>
            <div class="content-form">
              <span class="font-content">{{this.wechatNumber}}</span>
            </div>
          </div>
          <div class="info-item">
            <div class="title-form">
              <span style="float:right;margin-top:7%">所属单位：</span>
            </div>
            <div class="content-form">
              <span class="font-content">{{this.affiliatedUnit}}</span>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- 身份证 -->
    <div class="id-card">
      <div class="top-idcard">
        <span>身份证（正面）</span>
        <img :src="this.frontIdCard" style="width: 300px; height: 200px" />
      </div>
      <div class="bt-idcard">
        <span>身份证（反面）</span>
        <img :src="this.reverseIdCard" style="width: 300px; height: 200px" alt="身份证反面" />
        <!-- <el-image
          :src="srcUrl+this.reverseIdCard"
          alt="一寸照"
          style="height:200px;width:320px"
          fit="contain"
        >
          <div slot="error" class="image-slot">
            <i class="el-icon-picture-outline"></i>
          </div>
        </el-image>-->
      </div>
    </div>
    <!-- 审核记录 -->
    <div style="margin-top:100px">
      <div class="infoBox1">
        <span>审核记录</span>
      </div>
      <el-table :data="getdataTable" height="250" border style="width: 100%">
        <el-table-column prop="registrationTime" label="报名时间" width="180"></el-table-column>
        <el-table-column prop="applicationContent" label="报名内容" width="180"></el-table-column>
        <el-table-column prop="auditStatusName" label="审核情况" :formatter="StudentStatus"></el-table-column>
        <el-table-column prop="reviewedBy" label="审核人"></el-table-column>
        <el-table-column prop="auditTime" label="审核时间"></el-table-column>
      </el-table>
    </div>
    <!-- 学习记录 -->
    <div style="margin-top:100px">
      <div class="infoBox1">
        <span>学习记录</span>
      </div>
      <el-table :data="tableData" height="250" border style="width: 100%">
        <el-table-column prop="name" label="班级名称" width="180"></el-table-column>
        <el-table-column prop="typeName" label="培训类别" width="180"></el-table-column>
        <el-table-column prop="createTime" label="分班时间"></el-table-column>
        <el-table-column prop="days" label="学习周期"></el-table-column>
        <el-table-column prop="beginTime" label="学习开始时间"></el-table-column>
        <el-table-column prop="endTime" label="结业时间"></el-table-column>
        <el-table-column prop="master" label="班主任"></el-table-column>
      </el-table>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      dataTable: [],
      getdataTable: [],
      fit: "cover",
      tableData: [],
      datalist: null,
      pname: "",
      pgender: "",
      idNumber: "",
      personnelCategory: "",
      phoneNumber: "",
      creationTime: "",
      trainingCategory: "",
      trainingLevel: "",
      trainingType: "",
      socialSecurityAccount: "",
      wechatNumber: "",
      affiliatedUnit: "",
      pclass: "",
      personneStatus: "",
      srcUrl: "http://192.168.1.112:8070/vocational-training",
      photo: "",
      reverseIdCard: "",
      frontIdCard: "",
      fid: "",
    };
  },
  created() {
    console.log(this.$route.query.fid);
    this.peopleInfo();
  },

  methods: {
    StudentStatus(row, column) {
      if (row.auditStatus === "0") {
        return "未通过";
      } else {
        return "已通过";
      }
    },
    peopleInfo() {
      this.$http({
        url: this.$http.adornUrl("/sys/trainerPersonByID"),
        method: "get",
        params: this.$http.adornParams({
          fid: this.$route.query.fid,
        }),
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.dataList = data.data;
          this.pname = this.dataList.pname;
          this.pgender = this.dataList.pgender;
          this.idNumber = this.dataList.idNumber;
          this.personnelCategory = this.dataList.personnelCategory;
          this.phoneNumber = this.dataList.phoneNumber;
          this.creationTime = this.dataList.creationTime;
          this.trainingCategory = this.dataList.trainingCategory;
          this.trainingLevel = this.dataList.trainingLevel;
          this.trainingType = this.dataList.trainingType;
          this.socialSecurityAccount = this.dataList.socialSecurityAccount;
          this.wechatNumber = this.dataList.wechatNumber;
          this.affiliatedUnit = this.dataList.affiliatedUnit;
          this.pclass = this.dataList.pclass;
          this.personneStatus = this.dataList.state;
          this.photo = this.dataList.photo;
          this.reverseIdCard = this.dataList.reverseIdCard;
          this.frontIdCard = this.dataList.frontIdCard;
          this.fid = this.dataList.fid;
          console.log(this.reverseIdCard);
          console.log(this.pclass, 11);
          if (!this.pclass) {
            return (this.pclass = "未分班");
          }

          this.$http({
            url: this.$http.adornUrl("/sys/getClassList"),
            method: "get",
            params: this.$http.adornParams({
              studentId: this.fid,
            }),
          }).then(({ data }) => {
            if (data && data.code === 0) {
              this.tableData = data.list;
            }
          });
        }
      });
      this.getExamineLogList();
    },

    //审核记录
    getExamineLogList(pid) {
      console.log(11122);
      this.$http({
        url: this.$http.adornUrl("/sys/getExamineLogList"),
        method: "get",
        params: this.$http.adornParams({
          personId: this.$route.query.fid,
        }),
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.getdataTable = data.list;
        }
      });
    },
  },
};
</script>

<style lang="scss" scoped>
.infoBox {
  height: 40px;
  background: rgb(228, 228, 228);
  &:first-child {
    line-height: 40px;
    height: 40px;
    padding-left: 10px;
  }
}
.info-img {
  margin-top: 20px;
  display: flex;

  min-height: 400px;
}
.img-box1 {
  flex: 1;

  display: flex;
  box-sizing: border-box;
  padding-left: 10px;
  justify-content: space-between;
}
.info-form {
  width: 49%;
}
.info-form1 {
  width: 49%;
}
.id-card {
  display: flex;
  width: 80%;
  margin: 20px auto;
  > .top-idcard {
    width: 49%;
  }
  > .bt-idcard {
    width: 49%;
  }
}
.infoBox1 {
  height: 40px;

  &:first-child {
    line-height: 40px;
    height: 40px;
    padding-left: 10px;
  }
}
.info-item {
  display: flex;
  height: 10%;
  margin-bottom: 10px;
  .title-form {
    width: 30%;
  }
  .content-form {
    width: 60%;
    height: 100%;
    border: 1px solid #eee;
    display: flex;
    align-items: center;
    padding-left: 10px;
  }
}
</style>
