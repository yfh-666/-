<template>
  <div class="login_container">
    <div class="login_center_box">
      <el-form
        class="login_form"
        :model="loginForm"
        :rules="rules"
        ref="loginForms"
      >
        <div class="login_header">
          <img
            src="https://pic.yupi.icu/5563/202503151527812.png"
            class="login_avatar"
          />
          <h1>Hello</h1>
        </div>
        <h2>欢迎来到智能 AI 校园二手交易平台</h2>
        <el-form-item prop="userAccount">
          <el-input
            :prefix-icon="User"
            v-model="loginForm.userAccount"
            placeholder="请输入账号"
          ></el-input>
        </el-form-item>
        <el-form-item prop="userPassword">
          <el-input
            type="password"
            :prefix-icon="Lock"
            v-model="loginForm.userPassword"
            show-password
            placeholder="请输入密码"
          ></el-input>
        </el-form-item>
        <el-form-item>
          <el-button
            :loading="loading"
            type="primary"
            size="large"
            class="login_btn"
            @click="login"
            >登录
          </el-button>
          <el-button
            :loading="loading"
            type="success"
            size="large"
            class="login_btn"
            @click="register"
            >注册
          </el-button>
        </el-form-item>
      </el-form>
      <p class="login_footer">
        <i class="iconfont icon-banquan" style="font-size: 18px"></i>
        2025 小白条出品 |
        <a href="https://beian.miit.gov.cn/#/Integrated/index"
          >浙ICP备2023044565号-1</a
        >
        |<a href="https://beian.mps.gov.cn/#/query/webSearch">
          <img
            src="../../assets/images/logoPolice.png"
            style="height: 16px; width: 16px; margin: 5px 0px 0px 5px"
          />
          浙公网安备33028202001002号
        </a>
      </p>
    </div>
  </div>
</template>

<script setup>
import { ref } from "vue";
import { User, Lock } from "@element-plus/icons-vue";

// 你的其他逻辑代码
</script>

<style scoped lang="scss">
.login_container {
  width: 100vw;
  height: 100vh;
  background: linear-gradient(135deg, #f8e1f4 0%, #cbeefd 100%);
  display: flex;
  align-items: center;
  justify-content: center;
}

.login_center_box {
  margin: auto;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

.login_form {
  background: #fff;
  border-radius: 32px;
  box-shadow: 0 8px 32px 0 rgba(31, 38, 135, 0.15);
  padding: 48px 36px 24px 36px;
  min-width: 350px;
  display: flex;
  flex-direction: column;
  align-items: center;
  border: 3px dashed #ffd6e7;
  position: relative;
}

.login_header {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-bottom: 12px;
}

.login_avatar {
  width: 96px;
  height: 96px;
  border-radius: 50%;
  border: 4px solid #ffe0f0;
  margin-bottom: 8px;
  box-shadow: 0 2px 8px #ffd6e7;
  background: #fff;
}

.login_form h1 {
  color: #ff7eb9;
  font-size: 36px;
  font-family: "Comic Sans MS", "Comic Sans", cursive;
  margin: 0;
}

.login_form h2 {
  font-size: 18px;
  color: #7f8fa6;
  margin: 16px 0 24px 0;
  text-align: center;
  font-family: "Comic Sans MS", "Comic Sans", cursive;
}

.el-input__wrapper {
  border-radius: 20px !important;
  background: #fff0fa !important;
  border: 1.5px solid #ffd6e7 !important;
  box-shadow: none !important;
}

.login_btn {
  width: 120px;
  border-radius: 20px !important;
  font-size: 18px;
  margin: 0 10px;
  font-family: "Comic Sans MS", "Comic Sans", cursive;
}

.login_footer {
  margin-top: 24px;
  color: #b48ecb;
  font-size: 14px;
  text-align: center;
  font-family: "Comic Sans MS", "Comic Sans", cursive;
  a {
    color: #ff7eb9;
    margin: 0 4px;
  }
  img {
    vertical-align: middle;
  }
}
</style>
