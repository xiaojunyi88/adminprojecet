<template>
  <div>
    <el-form ref="form" :model="form" label-width="100px">
      <el-form-item label="二级数据名称">
        <el-input v-model="form.dataName"></el-input>
      </el-form-item>
      <el-form-item label="排序">
        <el-input v-model="form.solt"></el-input>
      </el-form-item>
      <div style="display:flex;height:36px">
        <div style="width:100px;text-align:right;font-size:14px;padding-right:12px">一级数据</div>
        <div style="border:1px soild #000">{{one}}</div>
      </div>

      <el-form-item label="备注">
        <el-input v-model="form.remarks"></el-input>
      </el-form-item>
      <div>
        <el-button type="success" @click="onSubmit() ">确认</el-button>
        <el-button type="info">返回</el-button>
      </div>
    </el-form>
  </div>
</template>
<script>
export default {
  data() {
    return {
      form: {
        dataName: "",
        solt: "",
        remarks: "",
        sort: ""
      },
      one: ""
    };
  },
  created() {
    this.form.dataName = this.$route.query.dataName;
    this.form.remarks = this.$route.query.remarks;
    this.form.sort = this.$route.query.sort;
    this.one = this.$route.query.one;
  },
  methods: {
    onSubmit() {
      this.$http({
        url: this.$http.adornUrl("/sys/editDataDictionary"),
        method: "post",
        data: this.$http.adornData({
          fid: this.$route.query.fid,
          dataName: this.form.dataName,
          sort: this.form.solt,
          remarks: this.form.remarks
        })
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.$message({
            message: "恭喜你，修改成功",
            type: "success"
          });

          this.$router.go(-1);
        } else {
          this.$message.error("错了哦，请重新填写");
        }
        this.dataListLoading = false;
      });
    }
  }
};
</script>

<style lang="scss" scoped>
</style>
