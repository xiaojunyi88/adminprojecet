<template>
  <div>
    <div style="height:30px;width:100%">
      <el-button @click="$router.back()" style="float:right">返回</el-button>
    </div>

    <table border="1" cellpadding="0" cellspacing="0" rules="none">
      <tr>
        <td align="center">姓名</td>
        <td align="center">{{this.name}}</td>
        <td align="center">身份证号</td>
        <td align="center">{{this.idCode}}</td>
      </tr>
      <tr>
        <td align="center">职业资格</td>
        <td align="center">{{this.dataName}}</td>
        <td align="center">技能等级</td>
        <td align="center">{{this.skill}}</td>
      </tr>

      <tr>
        <td align="center">证书编号</td>
        <td align="center">{{this.certificateCode}}</td>
        <td align="center">发证日期</td>
        <td align="center">{{this.licenceDate}}</td>
      </tr>
      <tr>
        <td align="center">数据责任单位</td>
        <td align="center" colspan="3">{{this.dataNames}}</td>
      </tr>
    </table>
  </div>
</template>

<script>
export default {
  data() {
    return {
      name: "",
      idCode: "",
      dataName: "",
      skill: "",
      licenceDate: "",
      certificateCode: "",
      dataNames: ""
    };
  },
  created() {
    this.peopleInfo();
  },
  methods: {
    peopleInfo() {
      this.$http({
        url: this.$http.adornUrl("/certificate/info"),
        method: "get",
        params: this.$http.adornParams({
          id: this.$route.query.id
        })
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.name = data.certificateVO.name;
          this.idCode = data.certificateVO.idCode;
          this.dataName = data.profession.dataName;
          this.skill = data.skill.dataName;
          this.licenceDate = data.certificateVO.licenceDate;
          this.certificateCode = data.certificateVO.certificateCode;
          this.dataNames = data.dataUnit.dataName;
        } else {
          this.accountabilityDataList = [];
          this.totalPage = 0;
        }
        this.dataListLoading = false;
      });
    }
  }
};
</script>

<style lang="scss" scoped>
table {
  width: 100%;
}
tr:last-of-type {
  height: 150px;
}
tr {
  border: solid 1px #a59e9e;
  height: 50px;
}
td {
  border: solid 1px #a59e9e;
  width: 150px;
}
// .mark {
//   font-size: 16px;
//   padding-top: 15px;
//   box-sizing: border-box;
//   background: floralwhite;
//   margin-top: 30px;
// }
</style>
