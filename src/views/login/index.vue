<template>
  <div class="login_container">
    <div class="cute-bg"></div>
    <div class="login_center">
      <el-form
        class="login_form"
        :model="loginForm"
        :rules="rules"
        ref="loginForms"
      >
        <div class="login_header">
          <h1>速易通</h1>
        </div>
        <h2>欢迎来到智能 AI 校园二手交易平台</h2>
        <el-form-item prop="userAccount" class="login_input">
          <el-input
            :prefix-icon="User"
            v-model="loginForm.userAccount"
            placeholder="请输入账号"
          ></el-input>
        </el-form-item>
        <el-form-item prop="userPassword" class="login_input">
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
            class="login_btn"
            @click="login"
            >登录
          </el-button>
          <el-button
            :loading="loading"
            type="success"
            class="login_btn"
            @click="register"
            >注册
          </el-button>
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>

<script setup lang="ts">
import { Lock, User } from "@element-plus/icons-vue";
import { onMounted, reactive, ref } from "vue";
// 引入用户相关的小仓库
// import userUserStore from "@/store/modules/user";
// 引入路由
import { useRoute, useRouter } from "vue-router";
// 引入ElementUI-Plus
import { ElMessage, ElNotification } from "element-plus";
// 引入时间函数
import { getTime } from "@/utils/time";
import useUserStore from "@/store/modules/user";
//收集账号与密码的数据
let loginForm = reactive({
  // userAccount: "yupi",
  userAccount: "xiaobaitiao",
  userPassword: "12345678"
});
// 获取loginForms组件
let loginForms = ref();
// 定义变量控制按钮加载效果
let loading = ref(false);
// 获取路由器
let $router = useRouter();
// 获取路由
let $route = useRoute();
let userStore = useUserStore();
// let userStore = userUserStore();
//登录按钮的回调
const login = async () => {
  // 保证全部表单相校验通过再发请求
  await loginForms.value.validate(async (valid: any) => {
    if (!valid) {
      ElMessage({
        type: "error",
        message: "表单参数不合法",
        duration: 1000
      });
    } else {
      // 点击按钮后发送登录请求，请求成功->首页展示数据的地方,请你失败->弹出登录失败的信息
      loading.value = true;
      try {
        await userStore.userLogin(loginForm);
        // 登录成功，跳转到展示数据的页面
        // 判断登录的时候，如果路由路径中有query参数，那么往query参数跳转，没有的话跳转到首页
        let redirect: any = $route.query.redirect;
        $router.push({ path: redirect || "/index" });
        // 提示信息
        ElNotification({
          type: "success",
          message: "欢迎回来",
          title: `HI,${getTime()}好`,
          duration: 3000
        });
      } catch (error) {
        // console.log(error);
        ElNotification({
          type: "error",
          message: "用户名或密码错误"
        });
      } finally {
        loading.value = false;
      }
    }
  });
};

onMounted(async () => {
  // console.log(1);
  // const res = await UserControllerService.userLoginUsingPost({
  //   userAccount: "yupi",
  //   userPassword: "12345678"
  // });
  // console.log(res);
});

// 注册按钮回调
const register = () => {
  $router.push("/register");
};
const validatorUserName = (_rule: any, value: any, callback: any) => {
  if (/^\w{4,15}$/.test(value)) {
    callback();
  } else {
    callback(new Error("账号长度应该在4位-15位之间"));
  }
};
const validatorPassword = (_rule: any, value: any, callback: any) => {
  if (/^\w{6,10}$/.test(value)) {
    callback();
  } else {
    callback(new Error("密码长度应该在6位-15位之间"));
  }
};
// 定义表单校验参数
const rules = {
  userAccount: [{ trigger: "change", validator: validatorUserName }],
  userPassword: [{ trigger: "change", validator: validatorPassword }]
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
  // 更多可爱圆圈
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
.login_container {
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
.login_center {
  z-index: 1;
  display: flex;
  align-items: center;
  justify-content: center;
  width: 100vw;
  height: 80vh;
}
.login_form {
  // min-width: 380px;
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
  .login_input {
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
  .login_btn {
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
.login_header {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-bottom: 8px;
  .login_logo {
    width: 96px;
    height: 96px;
    border-radius: 16px;
    margin-bottom: 8px;
    box-shadow: 0 2px 12px #b8d4ff;
  }
}
a {
  text-decoration: none;
  color: #ff8fab;
  transition: color 0.2s;
  &:hover {
    color: #ff5eae;
  }
}
.login_footer {
  position: fixed;
  bottom: 0px;
  left: 50%;
  transform: translate(-50%, -50%);
  z-index: 2;
  background: rgba(255, 255, 255, 0.7);
  border-radius: 12px;
  padding: 4px 16px;
  font-size: 14px;
  box-shadow: 0 2px 8px #f9d6e9;
  color: #ff8fab;
  a {
    color: #ff8fab;
    &:hover {
      color: #ff5eae;
    }
  }
}
</style>
