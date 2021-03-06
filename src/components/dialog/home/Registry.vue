<template lang="pug">
el-dialog(title="注册" :visible="isShowRegistry" width="26%" top="10vh" lock-scroll center custom-class="dialog r-dialog" :before-close="handleClose")
	el-form.form(:model="user" :rules="rules" ref="form" label-width="100px"  center status-ico)
		avatar-upload(:avatar.sync="user.avatar" :is-init="isInit")
		el-form-item(label="昵称" prop="name")
			el-input(type="text" v-model.trim="user.name" placeholder="2-8 位字符" clearable)
		el-form-item(label="账号" prop="account")
			el-input(type="text" v-model.trim="user.account" placeholder="手机号 / 邮箱" clearable @change="existHandle")
		el-form-item(label="密码" prop="password")
			el-input(type="password" v-model.trim="user.password" placeholder="6-12位 数字/字母/英文符号" clearable)
		el-form-item(label="确定密码" prop="checkpass")
			el-input(type="password" v-model.trim="user.checkpass" placeholder="6-12位 数字/字母/英文符号" clearable)
		el-form-item(label="所在地区")
			city-select(:address.sync="user.address" :is-init="isInit")
		el-form-item(label="验证码" prop="captcha")
			el-tooltip(effect="light" content="输入验证码的计算结果" placement="bottom" :disabled='disable')
				el-input.captcha(type="text" v-model.trim.number="user.captcha" @change='disable=true')
			captcha-img
	.dialog-footer(slot="footer")
		el-row
			el-button.registry(type="primary" @click="submitForm" v-loading.fullscreen.lock="isLoading" element-loading-text="正在注册") 注册
			el-button(@click="isInit=!isInit,$refs.form.resetFields()") 重置
		el-row
			el-button(type="text" @click="replaceRegistry") 登录已有账号
</template>

<script>
import { Vue, Component } from 'vue-property-decorator'
import CitySelect from '~/form/CitySelect'
import CaptchaImg from '~/form/CaptchaImg'
import AvatarUpload from '~/form/AvatarUpload'
import { mapState, mapMutations, mapActions } from 'vuex'
import {
	checkAccount,
	checkPassword,
	checkName,
	checkCaptcha
} from '@/utils/form/check'
import Regular from '@/utils/regular'
import tip from '@/utils/ui/tip'
import http from 'config/http'
@Component({
	components: {
		CitySelect,
		CaptchaImg,
		AvatarUpload
	},
	computed: {
		...mapState('dialog', ['isShowRegistry'])
	},
	methods: {
		...mapMutations('dialog', ['changeShowStatus', 'replaceRegistry']),
		...mapActions('user', ['registry', 'hasExisted']),
		...mapMutations('user', ['setAccount'])
	}
})
export default class HomeRegistry extends Vue {
	user = {
		name: '',
		account: '',
		password: '',
		checkpass: '',
		address: {
			province: '',
			city: '',
			county: '',
			code:''
		},
		captcha: '',
		avatar:null
	}

	selections = []
	disable = false
	isHad = false
	isLoading = false
	isInit = false
	rules = {
		name: checkName(),
		account: checkAccount,
		captcha: checkCaptcha,
		password: [
			{
				validator: (rule, value, callback) => {
					if (!value) {
						return callback(new Error('请输入密码'))
					}
					if (!Regular('password', value)) {
						return callback(new Error('密码为 6-12 位格式'))
					}
					if (this.user.checkpass) {
						this.$refs.form.validateField('checkpass')
					}
					callback()
				},
				trigger: 'blur'
			}
		],
		checkpass: [
			{
				validator: (rule, value, callback) => {
					if (!value) {
						return callback(new Error('请再次输入密码'))
					}
					if (!Regular('password', value)) {
						return callback(new Error('密码为 6-12 位格式'))
					}
					if (value !== this.user.password && this.user.password) {
						return callback(new Error('您两次输入的密码不一致'))
					}
					callback()
				},
				trigger: 'blur'
			}
		]
	}
	handleClose(done) {
		this.changeShowStatus({ name: 'Registry', status: false })
		done()
	}
	existHandle() {
		this.user.account &&
			this.$refs.form.validateField('account', errMsg => {
				if (errMsg) { return }
				this.hasExisted(this.user.account).then(isHad => {
					this.isHad = isHad
					if (isHad) { tip.warning('您输入的账号已被注册！', 2000) }
				})
			})
	}
	toRegistry(){
		this.registry(this.user)
			.then(response => {
				this.isLoading = false
				if (!response.success) {
					return tip.error(response.message)
				}
				this.setAccount(this.user.account)
        tip.success(response.message, 2000).then(this.replaceRegistry)
			}).catch(e=>{
				console.log(e)
				tip.error(e)
			})
	}
	submitForm() {
		this.$refs.form.validate(valid => {
			if (valid) {
				if (!this.user.address.province) {
					return tip.warning('请选择您所在的地区！')
				}
				if (this.isHad) {
					return tip.warning('您输入的账号已被注册！', 2000)
				}
				this.isLoading = true
				if (this.user.avatar && typeof this.user.avatar === 'object') {
					const reader = new FileReader()
					reader.readAsDataURL(this.user.avatar)
					reader.onloadend = ()=>{
						this.user.avatar = reader.result
						this.toRegistry()
					}
				}else{
					this.toRegistry()
				}
			} else {
				tip.error('请仔细核对信息！')
				return false
			}
		})
	}

}
</script>

<style lang="stylus">
.el-dialog__title
	font-size 150% !important
	letter-spacing 4px !important
	font-beautify() !important
.el-form-item__label
	letter-spacing 2px !important
	font-beautify() !important
.dialog
	position relative !important
	box-shadow 0 10px 50px rgb(233, 233, 233)
	padding 0.7vw 0 0 2vw
	border-radius 10px !important
	.dialog-footer
		margin-top -5vh !important
	.registry
		margin-right 3vw !important
		margin-bottom 3vh !important
	.captcha
		display inline-block !important
		width 58% !important
		text-align left  !important
		transform translateY(-1.5vh)   !important
.el-dialog__footer
	padding-bottom 10px !important
.r-dialog
	background radial-gradient(200px at left top,#fff 50%,#f6f6f6 50%) !important
	padding 0.7vw 0 0 3vw !important

</style>
