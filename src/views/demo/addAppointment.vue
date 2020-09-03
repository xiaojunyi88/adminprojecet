<template>
  <div>
    <el-breadcrumb separator-class="el-icon-arrow-right" class="separator">
      <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item :to="{ path: '/train' }">就业项目管理</el-breadcrumb-item>
      <el-breadcrumb-item>添加就业项目</el-breadcrumb-item>
    </el-breadcrumb>
    <el-form
      :model="ruleForm"
      :rules="rules"
      ref="ruleForm"
      label-width="150px"
      class="demo-ruleForm"
    >
      <el-form-item label="就业项目名称：" prop="customerIdCard">
        <el-input v-model="ruleForm.customerIdCard" placeholder="请填就业项目名称" style="width:30vw"></el-input>
      </el-form-item>

      <el-form-item label="排序：" prop="region">
        <el-select v-model="ruleForm.sort" placeholder="请选择就业项目" style="width:30vw">
          <el-option
            :label="item"
            :value="item"
            v-for="(item,index) in ruleForm.serviceItemSortList.slice(1) "
            :key="index"
          ></el-option>
        </el-select>
      </el-form-item>

      <el-form-item>
        <el-button type="primary" @click="submitForm('ruleForm')">确定</el-button>
        <el-button @click="resetForm('ruleForm')">重置</el-button>
        <el-button @click="fanhui" type="warning">返回</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>

<script>
export default {
  data() {
    return {
      timeList: [],
      sortNum: [],
      countSkillNum: "",
      timeId: "",
      lsh: "",
      lshTime: "",
      lshBussinessName: "",
      bussinessTime: null,
      showtime: null,
      ruleForm: {
        customerIdCard: "",
        customerTel: "",
        time: "",
        delivery: false,
        type: [],
        resource: "",
        sort: "",
        serviceItemSortList: []
      },
      rules: {
        customerIdCard: [
          { required: true, message: "请输入项目名称", trigger: "blur" }
        ],
        customerTel: [
          {
            type: "number",
            required: true,
            message: "请输入手机号",
            trigger: "blur"
          },
          {
            pattern: /^1[3456789]\d{9}$/,
            message: "手机号格式不对",
            trigger: "blur"
          }
        ],
        sort: [{ required: true, message: "请选择日期", trigger: "change" }],
        time: [
          {
            required: true,
            message: "请选择日期",
            trigger: "change"
          }
        ]
      }
    };
  },
  created() {
    this.openBussiness();
    this.openTime();
  },
  methods: {
    open() {
      if (this.lsh == "success") {
        this.$message({
          message: "恭喜你，这是一条成功消息",
          type: "success"
        });

        setTimeout(() => {
          this.$router.back(-1);
        }, 1000);
      } else if (this.lsh == "该培训技能名称已存在") {
        this.$message({
          message: this.lsh,
          type: "error"
        });
      }
    },
    // 查询排序
    openTime() {
      this.$http({
        url: this.$http.adornUrl("/sys/trainingSkill/countSkillNum"),
        method: "get",
        params: this.$http.adornParams({
          bussinessId: this.bussinessTime
        })
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.countSkillNum = data.countSkillNum;
          for (var i = 1; i <= this.countSkillNum + 1; i++) {
            this.sortNum.push(i);
          }
          this.ruleForm.serviceItemSortList = Object.keys(this.sortNum);
          console.log(this.ruleForm.serviceItemSortList.slice(1));
        } else {
          this.dataList = [];
          this.totalPage = 0;
        }
        this.dataListLoading = false;
      });
    },
    // 查询预约项目
    openBussiness() {
      this.$http({
        url: this.$http.adornUrl("/sys/trainingSkill/querySkillById"),
        method: "get",
        params: this.$http.adornParams({
          skillId: this.$route.query.skillId
        })
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.timeList = data.skillData;
          this.ruleForm.customerIdCard = this.timeList.skillName;
          this.ruleForm.sort = this.timeList.skillSort;
        } else {
          this.dataList = [];
          this.totalPage = 0;
        }
        this.dataListLoading = false;
      });
    },

    //返回
    fanhui() {
      this.$router.back(-1);
    },
    //提交信息
    openInfo() {
      if (this.$route.query.skillId) {
        this.$http({
          url: this.$http.adornUrl("/sys/trainingSkill/updateSkill"),
          method: "post",
          data: this.$http.adornData({
            skillId: this.$route.query.skillId,
            skillName: this.ruleForm.customerIdCard,
            skillSort: this.ruleForm.sort
          })
        }).then(({ data }) => {
          this.lsh = data.msg;
          this.open();
        });
      } else {
        this.$http({
          url: this.$http.adornUrl("/sys/trainingSkill/addSkill"),
          method: "post",
          data: this.$http.adornData({
            skillName: this.ruleForm.customerIdCard,
            skillSort: this.ruleForm.sort
          })
        }).then(({ data }) => {
          this.lsh = data.msg;
          this.open();
        });
      }
    },
    submitForm(formName) {
      this.$refs[formName].validate(valid => {
        if (valid) {
          this.openInfo();
        } else {
          console.log("error submit!!");
          return false;
        }
      });
    },
    resetForm(formName) {
      this.$refs[formName].resetFields();
    }
  }
};
</script>

<style lang="scss" scoped>
.separator {
  margin-bottom: 3vh;
}
.el-message-box {
  background-color: #e2f2ee !important;
}
</style>
