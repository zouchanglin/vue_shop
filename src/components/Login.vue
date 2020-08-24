<template>
  <div class="login-container">
    <div class="login-box">
      <!-- 头像 -->
      <div class="avatar-box">
        <img src="../assets/logo.png" alt />
      </div>
      <el-form
        ref="loginFormRef"
        label-width="0"
        class="login-form"
        :model="loginForm"
        :rules="loginRules"
      >
        <!-- 用户名 -->
        <el-form-item prop="username">
          <el-input
            prefix-icon="el-icon-user"
            v-model="loginForm.username"
          ></el-input>
        </el-form-item>
        <!-- 密码 -->
        <el-form-item prop="password">
          <el-input
            prefix-icon="el-icon-lock"
            v-model="loginForm.password"
            show-password
          ></el-input>
        </el-form-item>
        <!-- 按钮 -->
        <el-form-item class="login-btn">
          <el-button type="primary" @click="login('loginFormRef')"
            >登录
          </el-button>
          <el-button type="info" @click="restLoginForm('loginFormRef')"
            >重置
          </el-button>
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      // 登录数据绑定对象
      loginForm: {
        username: 'admin',
        password: '123456'
      },
      // 表单的验证规则对象
      loginRules: {
        // 验证用户名合法性
        username: [
          {
            required: true,
            message: '请输入用户名',
            trigger: 'blur'
          },
          {
            min: 3,
            max: 5,
            message: '长度在 3 到 5 个字符',
            trigger: 'blur'
          }
        ],
        // 验证密码合法性
        password: [
          {
            required: true,
            message: '请输入密码',
            trigger: 'blur'
          },
          {
            min: 6,
            max: 15,
            message: '长度在 6 到 15 个字符',
            trigger: 'blur'
          }
        ]
      }
    }
  },
  methods: {
    // 点击重置按钮、重置表单
    restLoginForm(formName) {
      console.log(this)
      this.$refs[formName].resetFields()
    },
    login(formName) {
      this.$refs[formName].validate(async valid => {
        console.log(valid)
        if (!valid) return
        const { data: ret } = await this.$http.post('login', this.loginForm)
        console.log('login -> ret', ret)
        if (ret.meta.status !== 200) {
          return this.$message.error('登录失败，请检查用户名和密码')
        }
        this.$message.success('登录成功')
        // 1、将登录成功之后的 token，保存到客户端的sessionStorage中
        // 1.1项目中出了登录之外的其他API接口，必须在登录之后才能访问
        // 1.2 token只应在当前网站打开期间生效，所以将 token保存在sessionStorage中
        window.sessionStorage.setItem('token', ret.data.token)
        // 通过编程式导航跳转到后台主页，路由地址是/home
        await this.$router.push('/home')
      })
    }
  },
  name: 'Login'
}
</script>

<style lang="less" scoped>
.login-container {
  background-color: #2b4b6b;
  height: 100%;
}

.login-box {
  width: 450px;
  height: 300px;
  background-color: #ffffff;
  border-radius: 5px;
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);

  .avatar-box {
    width: 130px;
    height: 130px;
    border: 2px solid #eeeeee;
    border-radius: 50%;
    padding: 10px;
    box-shadow: 0 0 5px;
    position: absolute;
    left: 50%;
    transform: translate(-50%, -50%);
    background-color: #ffffff;

    img {
      border-radius: 50%;
      width: 100%;
      height: 100%;
      background-color: #dddddd;
    }
  }

  .login-btn {
    display: flex;
    justify-content: flex-end;
  }

  .login-form {
    position: absolute;
    bottom: 0;
    width: 100%;
    padding: 0 20px;
    box-sizing: border-box;
  }
}
</style>
