<template>
  <div class="logincontainer">

    <div style="display: flex; width: 400px; height: 350px; margin: auto;
                position: absolute;
	              top: 0;
	              left: 0;
	              right: 0;
	              bottom: 0;">

<!--      <div style="flex: 1;">-->
<!--        <img src="../../assets/images/图书馆.png" style="width: 100%; height: 100%;">-->
<!--      </div>-->
      <div style="flex: 1; background-color: #FFFFFF;">
        <div style="text-align: center; font-weight: 600; font-size: 20px; margin: 15px; color: #000000">
          图 书 馆 管 理 系 统
        </div>
        <el-form
          ref="loginForm"
          :model="loginForm"
          :rules="rules"
          label-width="80px"
          :inline="false"
          size="normal"
          style="padding: 20px;"
        >
          <el-form-item prop="username" style="margin-bottom: 20px;">
            <el-input
              v-model="loginForm.username"
              placeholder="请输入用户名"
              clearable
            >
              <svg-icon slot="prefix" icon-class="user" class="el-input__icon input-icon"/>
            </el-input>
          </el-form-item>
          <el-form-item prop="password" style="margin-bottom: 20px;">
            <el-input
              v-model="loginForm.password"
              type="password"
              placeholder="请输入密码"
              clearable
            >
              <svg-icon slot="prefix" icon-class="password" class="el-input__icon input-icon"/>
            </el-input>
          </el-form-item>
          <el-form-item prop="userType" style="text-align: left">
            <el-radio-group v-model="loginForm.userType">
              <el-radio :label="0">读者</el-radio>
              <el-radio :label="1">管理员</el-radio>
            </el-radio-group>
          </el-form-item>
          <el-button
            class="but"
            type="primary"
            @click="onSubmit"
          >登 录
          </el-button>


          <div class="but1">
            没有账号，点击
            <el-button
              type="text"
              @click="registerBtn"
            >注册
            </el-button>
          </div>

        </el-form>
      </div>

    </div>

    <!-- 注册弹框 -->
    <sys-dialog
      :title="dialog.title"
      :width="dialog.width"
      :height="dialog.height"
      :visible="dialog.visible"
      @onClose="onClose"
      @onConfirm="onConfirm"
    >
      <div slot="content">
        <el-form
          ref="addRef"
          :model="addModel"
          :rules="registeRules"
          label-width="80px"
          size="small"
          style="margin-right: 30px"
        >
          <el-row>
            <el-col :span="12" :offset="0">
              <el-form-item prop="learnNum" label="姓名">
                <el-input v-model="addModel.learnNum"/>
              </el-form-item>
            </el-col>
            <el-col :span="12" :offset="0">
              <el-form-item prop="phone" label="电话">
                <el-input v-model="addModel.phone" maxlength="11"/>
              </el-form-item>
            </el-col>
          </el-row>
          <el-row>
            <el-col :span="12" :offset="0">
              <el-form-item prop="username" label="学号">
                <el-input v-model="addModel.username"/>
              </el-form-item>
            </el-col>
            <el-col :span="12" :offset="0">
              <el-form-item prop="className" label="班级">
                <el-input v-model="addModel.className"/>
              </el-form-item>
            </el-col>
          </el-row>
          <el-row>
            <el-col :span="12" :offset="0">
              <el-form-item prop="idCard" label="身份证">
                <el-input v-model="addModel.idCard" maxlength="18"/>
              </el-form-item>
            </el-col>
            <el-col :span="12" :offset="0">
              <el-form-item prop="password" label="密码">
                <el-input v-model="addModel.password" type="password"/>
              </el-form-item>
            </el-col>
          </el-row>
          <el-row>
            <el-col :span="12" :offset="0">
              <el-form-item prop="confirmPassword" label="确认密码">
                <el-input v-model="addModel.confirmPassword" type="password"/>
              </el-form-item>
            </el-col>
            <el-col :span="12" :offset="0">
              <el-form-item label="性别">
                <el-radio-group v-model="addModel.sex">
                  <el-radio :label="'0'">男</el-radio>
                  <el-radio :label="'1'">女</el-radio>
                </el-radio-group>
              </el-form-item>
            </el-col>
          </el-row>
        </el-form>
      </div>
    </sys-dialog>
  </div>
</template>

<script>
import { setUserType } from '@/utils/auth'
import { registerApi } from '@/api/reader'
import SysDialog from '@/components/dialog/SysDialog.vue'

export default {
  components: {
    SysDialog
  },
  data() {
    return {
      registeRules: {
        learnNum: [{ required: true, message: '请填写姓名', trigger: 'blur' }],
        username: [{ required: true, message: '请填写学号', trigger: 'blur' }],
        idCard: [
          { required: true, message: '请填写身份证号', trigger: 'blur' }
        ],
        phone: [{ pattern: /^1[3|4|5|6|7|8|9][0-9]\d{8}$/, required: true, message: '请输入正确的手机号码', trigger: 'blur' }],
        password: [{ required: true, message: '请填写密码', trigger: 'blur' }],
        confirmPassword: [
          { required: true, message: '请填写确认密码', trigger: 'blur' }
        ],
        className: [{ required: true, message: '请填写班级', trigger: 'blur' }]
      },
      // 表单属性
      addModel: {
        type: '',
        readerId: '',
        learnNum: '',
        username: '',
        idCard: '',
        sex: '',
        phone: '',
        password: '',
        confirmPassword: '',

        className: ''
      },
      loading: false,
      dialog: {
        title: '读者注册',
        width: 650,
        height: 250,
        visible: false
      },
      // 登录表单绑定数据源
      loginForm: {
        username: '',
        password: '',
        userType: '' // 0：读者  1： 管理员
      },
      // 表单验证规则
      rules: {
        username: [
          {
            trigger: 'change',
            required: true,
            message: '请输入用户名'
          }
        ],
        password: [
          {
            trigger: 'change',
            required: true,
            message: '请输入密码'
          }
        ],
        userType: [
          {
            trigger: 'change',
            required: true,
            message: '请选择用户类型'
          }
        ]
      }
    }
  },
  methods: {
    onClose() {
      this.dialog.visible = false
    },
    onConfirm() {
      // eslint-disable-next-line eqeqeq
      if (this.addModel.confirmPassword != this.addModel.password) {
        this.$message.warning('两次密码不一致!')
        return
      }
      this.$refs.addRef.validate(async(valid) => {
        if (valid) {
          const res = await registerApi(this.addModel)
          // eslint-disable-next-line eqeqeq
          if (res && res.code == 200) {
            this.$message.success(res.msg)
            this.dialog.visible = false
          }
        }
      })
    },
    // 读者注册
    registerBtn() {
      // 清空表单
      this.$resetForm('addRef', this.addModel)
      this.dialog.visible = true
    },
    // 登录提交事件
    onSubmit() {
      // 表单验证
      this.$refs.loginForm.validate((valid) => {
        // 验证通过才提交表单
        if (valid) {
          this.loading = true
          setUserType(this.loginForm.userType)
          // 调用store里面的login方法
          this.$store
            .dispatch('user/login', this.loginForm)
            .then(() => {
              // 跳转路由
              this.$router.push({ path: this.redirect || '/' })
              this.loading = false
            })
            .catch(() => {
              this.loading = false
            })
        }
      })
    }
  }
}
</script>

<style scoped>
.logincontainer {
  height: 100%;
  overflow: hidden;
  /*background-color: #FA9173;*/
  background-image: url("../../assets/images/图书.jpg");
  background-size: 100%;
}

.loginTitle {
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 24px;
  font-weight: 600;
  color: #26bd9a
}

.but {
  width: 100%;
  color: black;
  font-size: 20px;
}
.but1 {
  font-size: 16px;
  float: right;
}

.logincontainer ::v-deep .el-form-item__content {
  margin-left: 0px !important;
}
</style>
