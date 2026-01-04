<template>
  <view class="page">
    <view class="card">
      <view class="avatar green"></view>
      <view class="brand">快乐社团平台</view>
      <view class="subtitle">创建您的账号</view>
      <view class="group">
        <input class="input" v-model="form.account" placeholder="用户名" @blur="validate('account')" />
        <text v-if="errors.account" class="err">{{ errors.account }}</text>
      </view>
      <view class="group">
        <input class="input" v-model="form.name" placeholder="姓名" @blur="validate('name')" />
        <text v-if="errors.name" class="err">{{ errors.name }}</text>
      </view>
      <view class="group">
        <input class="input" v-model="form.student_no" placeholder="学号" @blur="validate('student_no')" />
        <text v-if="errors.student_no" class="err">{{ errors.student_no }}</text>
      </view>
      <view class="group">
        <input class="input" v-model="extra.college" placeholder="学院" @blur="validate('college')" />
        <text v-if="errors.college" class="err">{{ errors.college }}</text>
      </view>
      <view class="group">
        <view class="gender">
          <button class="gbtn" :class="extra.gender==='male'?'on':''" @tap="extra.gender='male'">男</button>
          <button class="gbtn" :class="extra.gender==='female'?'on':''" @tap="extra.gender='female'">女</button>
          <button class="gbtn" :class="extra.gender==='secret'?'on':''" @tap="extra.gender='secret'">保密</button>
        </view>
      </view>
      <view class="group">
        <view class="input-wrapper">
          <input class="input-clean" :password="hidePwd" v-model="form.password" placeholder="密码（至少6位）" @blur="validate('password')" />
          <image class="eye-icon" :src="hidePwd ? iconClosed : iconOpen" @tap="hidePwd=!hidePwd" />
        </view>
        <text v-if="errors.password" class="err">{{ errors.password }}</text>
      </view>
      <view class="group">
        <input class="input" :password="true" v-model="extra.confirm" placeholder="确认密码" @blur="validate('confirm')" />
        <text v-if="errors.confirm" class="err">{{ errors.confirm }}</text>
      </view>
      <button class="primary" :loading="loading" :disabled="loading" @tap="submit">注册</button>
      <view class="tip">已有账号？<text class="action" @tap="goLogin">立即登录</text></view>
    </view>
  </view>
</template>

<script>
import { request, setToken } from '../../utils/request.js'
import { switchTo, go } from '../../utils/router.js'
export default {
  data() {
    return {
      form: { account: '', password: '', name: '', student_no: '' },
      extra: { college: '', gender: 'secret', confirm: '' },
      errors: { account: '', password: '', confirm: '', name: '', student_no: '', college: '' },
      hidePwd: true,
      loading: false,
      iconOpen: 'data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9IjAgMCAyNCAyNCIgZmlsbD0ibm9uZSIgc3Ryb2tlPSIjOTk5IiBzdHJva2Utd2lkdGg9IjIiIHN0cm9rZS1saW5lY2FwPSJyb3VuZCIgc3Ryb2tlLWxpbmVqb2luPSJyb3VuZCI+PHBhdGggZD0iTTEgMTJzNC04IDExLTggMTEgOCAxMSA4LTQgOC0xMSA4LTExLTgtMTEtOHoiPjwvcGF0aD48Y2lyY2xlIGN4PSIxMiIgY3k9IjEyIiByPSIzIj48L2NpcmNsZT48L3N2Zz4=',
      iconClosed: 'data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9IjAgMCAyNCAyNCIgZmlsbD0ibm9uZSIgc3Ryb2tlPSIjOTk5IiBzdHJva2Utd2lkdGg9IjIiIHN0cm9rZS1saW5lY2FwPSJyb3VuZCIgc3Ryb2tlLWxpbmVqb2luPSJyb3VuZCI+PHBhdGggZD0iTTE3Ljk0IDE3Ljk0QTEwLjA3IDEwLjA3IDAgMCAxIDEyIDIwYy03IDAtMTEtOC0xMS04YTE4LjQ1IDE4LjQ1IDAgMCAxIDUuMDYtNS45NE05LjkgNC4yNEE5LjEyIDkuMTIgMCAwIDEgMTIgNGM3IDAgMTEgOCAxMSA4YTE4LjUgMTguNSAwIDAgMS0yLjE2IDMuMTltLTYuNzItMS4wNy0yLjMgMi4zIj48L3BhdGg+PGxpbmUgeDE9IjEiIHkxPSIxIiB4Mj0iMjMiIHkyPSIyMyI+PC9saW5lPjwvc3ZnPg=='
    }
  },
  methods: {
    validate(field) {
      if (field === 'account') {
        const v = (this.form.account || '').trim()
        this.errors.account = v.length >= 4 ? '' : '至少4位字符'
      }
      if (field === 'password') {
        const v = this.form.password || ''
        this.errors.password = v.length >= 6 ? '' : '至少6位字符'
      }
      if (field === 'confirm') {
        const v = this.extra.confirm || ''
        this.errors.confirm = v === this.form.password ? '' : '两次密码不一致'
      }
      if (field === 'name') {
        this.errors.name = (this.form.name || '').trim() ? '' : '姓名不能为空'
      }
      if (field === 'student_no') {
        this.errors.student_no = (this.form.student_no || '').trim() ? '' : '学号不能为空'
      }
      if (field === 'college') {
        this.errors.college = (this.extra.college || '').trim() ? '' : '学院不能为空'
      }
    },
    async submit() {
      this.validate('account'); this.validate('password'); this.validate('confirm');
      this.validate('name'); this.validate('student_no'); this.validate('college');
      if (this.errors.account || this.errors.password || this.errors.confirm || this.errors.name || this.errors.student_no || this.errors.college) return
      this.loading = true
      try {
        const data = await request({ url: '/public/register', method: 'POST', data: { 
          account: this.form.account, 
          password: this.form.password, 
          name: this.form.name, 
          student_no: this.form.student_no,
          gender: this.extra.gender, 
          college: this.extra.college 
        } })
        if (data && data.token) {
          setToken(data.token)
          uni.showToast({ title: '注册成功' })
          switchTo('clubsList')
        } else {
          uni.showToast({ title: '注册失败', icon: 'none' })
        }
      } catch(e) { uni.showToast({ title: e.msg || '注册失败', icon: 'none' }) }
      this.loading = false
    },
    goLogin() { go('login', {}, { replace: true }) }
  }
}
</script>

<style>
.page { min-height:100vh; display:flex; flex-direction:column; background:#f7f8fa; padding:0 16px }
.header { height: 88rpx; background: #7e78ff; display:flex; align-items:center; padding:0 12px; border-bottom-left-radius:12px; border-bottom-right-radius:12px }
.htitle { color:#fff; font-weight:600 }
.card { width:92%; max-width:380px; margin:20px auto 24px auto; background:#fff; border-radius:14px; box-shadow: 0 8px 20px rgba(0,0,0,0.06); padding:18px; display:flex; flex-direction:column; align-items:center }
.avatar { width:72px; height:72px; border-radius:50%; background:#7e78ff; opacity:0.9 }
.green { background:#2ecc71 }
.brand { margin:8px 0 6px 0; color:#333; font-weight:600; font-size:18px }
.subtitle { margin-bottom:12px; color:#666; font-size:13px }
.group { width:100%; margin-bottom:12px }
.label { display:block; font-size:14px; color:#333; margin-bottom:6px }
.row { display:flex; align-items:center; gap:8px }
.input { width:90%; border:1px solid #e6e6e6; border-radius:12px; padding:12px 14px; font-size:14px; background:#fafafa }
.input:focus { border-color:#007AFF; background:#fff }
.flex { flex:1 }
.toggle { font-size:12px; padding:8px 10px; background:#f5f7ff; color:#2e5cff; border-radius:10px }
.gender { width:50%; display:flex; gap:6px; justify-content:start }
.gbtn { padding:6px 10px; border:1px solid #e0e0ff; border-radius:14px; background:#fff; color:#7e78ff; font-size:12px }
.on { background:#e9e7ff }
.err { margin-top:6px; font-size:12px; color:#e34a4a }
.primary { width:100%; margin-top:8px; background:#2ecc71; color:#fff; border:none; border-radius:12px; font-weight:600 }
.tip { margin-top:12px; color:#666 }
.action { color:#7e78ff }
.input-wrapper { display:flex; align-items:center; width:90%; border:1px solid #e6e6e6; border-radius:12px; background:#fafafa; padding:0 14px }
.input-wrapper:focus-within { border-color:#007AFF; background:#fff }
.input-clean { flex:1; height:46px; font-size:14px; background:transparent; border:none }
.eye-icon { width:20px; height:20px; margin-left:8px; flex-shrink:0 }
</style>
