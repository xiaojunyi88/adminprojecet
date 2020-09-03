<template>
  <div class="site-wrapper site-page--login">
    <div class="login-main">
      <h3 class="login-title">职业培训报名系统</h3>
      <el-form
        :model="dataForm"
        :rules="dataRule"
        ref="dataForm"
        @keyup.enter.native="dataFormSubmit()"
        status-icon
      >
        <el-form-item prop="userName">
          <el-input v-model="dataForm.userName" placeholder="帐号"></el-input>
        </el-form-item>
        <el-form-item prop="password">
          <el-input v-model="dataForm.password" type="password" placeholder="密码"></el-input>
        </el-form-item>
        <el-form-item prop="captcha">
          <el-row :gutter="20">
            <el-col :span="14">
              <el-input v-model="dataForm.captcha" placeholder="验证码"></el-input>
            </el-col>
            <el-col :span="10" class="login-captcha">
              <img :src="captchaPath" @click="getCaptcha()" alt />
            </el-col>
          </el-row>
        </el-form-item>
        <el-form-item>
          <el-button class="login-btn-submit" type="primary" @click="dataFormSubmit()">登录</el-button>
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>

<script>
import { getUUID } from "@/utils";
export default {
  data() {
    return {
      dataForm: {
        userName: "",
        password: "",
        uuid: "",
        captcha: "",
      },
      dataRule: {
        userName: [
          { required: true, message: "帐号不能为空", trigger: "blur" },
        ],
        password: [
          { required: true, message: "密码不能为空", trigger: "blur" },
        ],
        captcha: [
          { required: true, message: "验证码不能为空", trigger: "blur" },
        ],
      },
      captchaPath: "",
    };
  },
  created() {
    this.getCaptcha();
  },
  methods: {
    // 提交表单
    dataFormSubmit() {
      this.$refs["dataForm"].validate((valid) => {
        if (valid) {
          this.$http({
            url: this.$http.adornUrl("/sys/login"),
            method: "post",
            data: this.$http.adornData({
              username: this.dataForm.userName,
              password: this.dataForm.password,
              uuid: this.dataForm.uuid,
              captcha: this.dataForm.captcha,
            }),
          }).then(({ data }) => {
            if (data && data.code === 0) {
              this.$cookie.set("token", data.token);
              this.$router.replace({ name: "home" });
              console.log(data);
            } else {
              this.getCaptcha();
              this.$message.error(data.msg);
            }
          });
        }
      });
    },
    // 获取验证码
    getCaptcha() {
      this.dataForm.uuid = getUUID();
      this.captchaPath = this.$http.adornUrl(
        `/captcha.jpg?uuid=${this.dataForm.uuid}`
      );
    },
  },
};
</script>

<style lang="scss">
.site-wrapper.site-page--login {
  position: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  background-color: #00cc99;
  overflow: hidden;
}
.login-main {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 4.21rem;
  height: 3.5rem;
  margin: 0 auto;
  padding: 0.1rem 0.5rem;
  background-color: #fff;
  border-radius: 15px;
}
.login-title {
  font-size: 2.5vh;
  text-align: center;
  line-height: 0;
  padding-top: 1vh;
  padding-bottom: 1vh;
  color: #1abc9c;
}
.login-captcha {
  overflow: hidden;
  > img {
    width: 100%;
    cursor: pointer;
  }
}
.login-btn-submit {
  width: 100%;
  background: #1abc9c;
  height: 0.5rem;
  font-size: 2vh;
}
</style>
