<template>
  <div class="register_container">
    <div class="cute-bg"></div>
    <div class="register_center">
      <el-form
        class="register_form"
        :model="registerForm"
        :rules="rules"
        ref="registerForms"
      >
        <div class="register_header">
          <h1>速易通</h1>
        </div>
        <h2>注册</h2>
        <el-form-item prop="userAccount" class="register_input">
          <el-input
            placeholder="请输入您的账号"
            :prefix-icon="User"
            v-model="registerForm.userAccount"
          ></el-input>
        </el-form-item>
        <el-form-item prop="userPassword" class="register_input">
          <el-input
            placeholder="请输入您的密码"
            type="password"
            :prefix-icon="Lock"
            v-model="registerForm.userPassword"
            show-password
          ></el-input>
        </el-form-item>
        <el-form-item prop="checkPassword" class="register_input">
          <el-input
            placeholder="请再次输入您的密码"
            type="password"
            :prefix-icon="Lock"
            v-model="registerForm.checkPassword"
            show-password
          ></el-input>
        </el-form-item>
        <el-form-item>
          <el-button
            :loading="loading"
            type="success"
            class="register_btn"
            @click="register"
            >注册
          </el-button>
          <el-button type="primary" class="back_login_btn" @click="backToLogin"
            >返回登录
          </el-button>
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>

<script setup lang="ts">
import { Lock, User } from "@element-plus/icons-vue";
import { reactive, ref } from "vue";
// 引入用户相关的小仓库
// import userUserStore from "@/store/modules/user";
// 引入路由
import { useRouter } from "vue-router";
// 引入ElementUI-Plus
import { ElMessage } from "element-plus";
// 引入时间函数
// import { getTime } from "@/utils/time";
import { userRegisterUsingPost } from "@/api/userController";
//收集账号与密码的数据
let registerForm = reactive({
  userAccount: "",
  userPassword: "",
  checkPassword: ""
});

// 获取registerForms组件
let registerForms = ref();
// 定义变量控制按钮加载效果
let loading = ref(false);
// 获取路由器
let $router = useRouter();
// // 获取路由
// let $route = useRoute();
// let userStore = userUserStore();
//登录按钮的回调
const register = async () => {
  // 保证全部表单相校验通过再发请求
  const valid = registerForms.value.validate();
  if (!valid) {
    ElMessage({
      type: "error",
      message: "表单参数不合法",
      duration: 1000
    });
  }

  // 那么开始注册
  let result: any = await userRegisterUsingPost(registerForm);
  if (result.code == 200) {
    ElMessage.success({
      message: "注册用户成功",
      duration: 1500
    });
    // 注册成功，跳转到登录页面
    $router.push("/login");
  } else {
    ElMessage.error({
      message: result.message,
      duration: 1500
    });
  }
  // console.log(result);
};
// 返回登录按钮的回调
const backToLogin = () => {
  $router.push("/login");
};
const validatorUserName = (_rule: any, value: any, callback: any) => {
  if (/^\w{4,15}$/.test(value)) {
    callback();
  } else {
    callback(new Error("账号长度应该在4位-15位之间"));
  }
};
const validatorPassword = (_rule: any, value: any, callback: any) => {
  if (/^\w{8,10}$/.test(value)) {
    callback();
  } else {
    callback(new Error("密码长度应该在8位-15位之间"));
  }
};
const validatorCheckPassword = (_rule: any, value: any, callback: any) => {
  if (registerForm.userPassword != value) {
    callback(new Error("两次输入的密码不一致"));
  }
};
// 定义表单校验参数
const rules = {
  userAccount: [{ trigger: "change", validator: validatorUserName }],
  userPassword: [{ trigger: "change", validator: validatorPassword }],
  checkPassword: [{ trigger: "change", validator: validatorCheckPassword }]
};
</script>

<style scoped lang="scss">
// 可爱背景圆圈
.cute-bg {
  position: absolute;
  width: 100vw;
  height: 100vh;
  z-index: 0;
  pointer-events: none;
  &::before,
  &::after {
    content: "";
    position: absolute;
    border-radius: 50%;
    filter: blur(30px);
    opacity: 0.7;
  }
  &::before {
    width: 300px;
    height: 300px;
    left: 8vw;
    top: 8vh;
    background: #ffe0f0;
  }
  &::after {
    width: 400px;
    height: 400px;
    right: 8vw;
    bottom: 8vh;
    background: #d0f4de;
  }
  .circle1 {
    position: absolute;
    width: 120px;
    height: 120px;
    left: 60vw;
    top: 10vh;
    background: #f9d6e9;
    border-radius: 50%;
    filter: blur(20px);
    opacity: 0.5;
  }
  .circle2 {
    position: absolute;
    width: 80px;
    height: 80px;
    right: 20vw;
    bottom: 20vh;
    background: #b5ead7;
    border-radius: 50%;
    filter: blur(16px);
    opacity: 0.5;
  }
}
.register_container {
  width: 100vw;
  height: 100vh;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  background: linear-gradient(135deg, #f9d6e9 0%, #b5ead7 100%);
  position: relative;
  overflow: hidden;
}
.register_center {
  z-index: 1;
  display: flex;
  align-items: center;
  justify-content: center;
  width: 100vw;
  height: 80vh;
}
.register_form {
  min-width: 380px;
  max-width: 420px;
  background: #fff;
  border-radius: 32px;
  box-shadow: 0 12px 48px 0 rgba(31, 38, 135, 0.16);
  padding: 48px 36px 32px 36px;
  display: flex;
  flex-direction: column;
  align-items: center;
  transition: box-shadow 0.2s;
  position: relative;
  .register_input {
    width: 100%;
  }
  h1 {
    color: #ff8fab;
    font-size: 44px;
    margin-bottom: 8px;
    font-weight: bold;
    letter-spacing: 2px;
  }
  h2 {
    font-size: 20px;
    color: #7b7b7b;
    margin: 20px 0px 24px 0px;
    text-align: center;
    font-weight: 400;
  }
  .register_btn,
  .back_login_btn {
    width: 100px;
    margin: 0 10px;
    border-radius: 24px;
    font-weight: bold;
    font-size: 16px;
    box-shadow: 0 2px 8px #f9d6e9;
    transition: box-shadow 0.2s, background 0.2s;
    &:hover {
      box-shadow: 0 4px 16px #ffb3c6;
    }
  }
}
.register_header {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-bottom: 8px;
  .register_logo {
    width: 96px;
    height: 96px;
    border-radius: 16px;
    margin-bottom: 8px;
    box-shadow: 0 2px 12px #b8d4ff;
  }
}
</style>
