<template>
  <div class="login-password" v-loading="loginLoading">
    <div class="login-title">登陆</div>
    <el-form :rules="rules" ref="form" :model="form">
      <el-form-item class="input-box" prop="telephone">
        <i class="iconfont input-iconfont icon-phone"></i>
        <el-input :maxlength="11" placeholder="手机号" v-model="form.telephone"></el-input>
      </el-form-item>
      <el-form-item class="input-box" prop="password">
        <i class="iconfont input-iconfont icon-lock"></i>
        <el-input :maxlength="16" type="password" placeholder="密码" v-model="form.password"></el-input>
      </el-form-item>
    </el-form>
    <el-button @click="submit" class="submit-button" type="primary">登陆</el-button>
  </div>
</template>
<script>
import md5 from '@/assets/js/md5.js'
import {INIT_LOGIN} from '@/vuex/actions_types'
import {UPDATE_LOGIN_LOADING} from '@/vuex/mutations_types'
export default {
  data () {
    return {
      form:{
        telephone:'',
        password:'',
        logintype:'pc'
      },
      rules:{
        telephone: [
          { required: true, message: '请输入手机号码', trigger: 'blur' },
          { min: 11, max: 11, message: '手机位数不正确', trigger: 'blur' }
        ],
        password: [
          { required: true, message: '请输入密码', trigger: 'blur' }
        ],
      }
    }
  },
  computed:{
    loginLoading(){
      return this.$store.state.login.loginLoading
    }
  },
  methods:{
    submit(){
      this.$refs['form'].validate((valid) => {
          if (valid) {
            this.$store.commit(UPDATE_LOGIN_LOADING,true)
            this.$api.login.login({
              telephone:this.form.telephone,
              password:md5.hexMD5(this.form.password),
              logintype:'pc'
            })
              .then(res => {
                if(res.data.error === '0'){
                  this.$store.dispatch(INIT_LOGIN,res.data.data)
                }
                else{
                  this.$store.commit(UPDATE_LOGIN_LOADING,false)
                }
              })
              .catch(err => {
                this.$store.commit(UPDATE_LOGIN_LOADING,false)
              })
          } else {
            return false;
          }
        });
    }
  }
}
</script>
<style lang="stylus" rel="stylesheet/stylus" scoped>

</style>
