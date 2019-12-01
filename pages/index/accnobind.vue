<template>
	<view class="body">
		<view class="input-box">
			<text class="text">姓名</text>
			<input type="text" v-model="name"
			class="common-input"  
			placeholder="请输入姓名"/>
		</view>
		
		<view class="input-box">
			<text class="text">档口编号</text>
			<input type="text" v-model="stallNumber"
			class="common-input" 
			placeholder="请输入档口编号"/>
		</view>
		
		<view class="input-box">
			<text class="text">手机号</text>
			<input type="text" v-model="phoneNumber"
			class="common-input" 
			placeholder="请输入手机号"/>
		</view>
		
		<view class="input-box">
			<text class="text">验证码</text>
			<input type="text" v-model="validateNum"
			class="common-input validate-input" 
			placeholder="请输入验证码"/>
			<view class="validator" @tap="getCheckCode">
				<view class="validate-text">
					{{!codetime?'获取验证码':codetime + ' s'}}
				</view>
			</view>
		</view>
		
		<view>
			<label class="checkbox"  @click="check = !check">
				<checkbox :checked="check" />
				<text>已阅读并同意</text><navigator 
				url="/pages/index/agreement" hover-class="navigator-hover" class="link" open-type="navigate">xxx协议</navigator>
				
			</label>
		</view>
		
		<button type="primary" 
		class="user-set-btn " 
		:class="{'user-set-btn-disable':disabled}"
		:disabled="disabled"
		@tap="submit">完成绑定</button>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				name: '',
				stallNumber: '',
				phoneNumber: '',
				validateNum:'',
				disabled: false,
				codetime:0,
				check:true,
				//验证规则
				rules:{
					phoneNumber:[
						{
							rule:/^(13[0-9]|14[5|7]|15[0|1|2|3|5|6|7|8|9]|18[0|1|2|3|5|6|7|8|9])\d{8}$/,
							msg:"请输入正确的11位手机号码"
						}
					],
					name:[
						{
							rule:/^[\u4e00-\u9fa5]{0,}$/,
							msg:"请输入正确的中文姓名"
						}
					]
				}
			}
		},
		// watch:{
		// 	name(){
		// 		this.change();
		// 	},
		// 	stallNumber(){
		// 		this.change();
		// 	},
		// 	phoneNumber(){
		// 		this.change();
		// 	},
		// 	validateNum(){
		// 		this.change();
		// 	}
		// },
		onLoad() {

		},
		methods: {
			//监听输入框
			change() {
				if(this.name && this.stallNumber && this.phoneNumber && this.validateNum) {
					this.disabled = false;
					return;
				}
				this.disabled = true;
			},
			//表单验证
			validate(key){
				var check = true;
				this.rules[key].forEach((v) => {
					if(!(v.rule).test(this[key])) {
						uni.showToast({
							title: v.msg,
							icon:'none'
						});
						check = false;
						return false;
					}
				})
				return check;
			},
			
			//提交表单
			submit() {
				//同意协议
				if(!this.check) {
					uni.showToast({
						title:"请阅读并同意协议",
						icon:'none'
					})
				}
				console.log('tap');
				//验证姓名
				console.log(this.validate('name'));
				//验证手机号
				console.log(this.validate('phoneNumber'));
				
				//绑定中
				uni.showLoading({
					title:'绑定中...',
					mask:true
				});
				
				//绑定完成
				setTimeout(()=>{
					uni.hideLoading()
				},3000);
				
			},
			
			//获取验证码
			getCheckCode() {
				if (this.codetime > 0) {
					uni.showToast({
						title:'不能重复获取验证码',
						icon: 'none'
					})
					return;
				}
				
				//验证手机号合法性
				if (!this.validate('phoneNumber')) {
					return;
				};
				
				//请求服务器，发送验证码
				
				//发送成功，开启倒计时
				this.codetime = 2;
				let timer = setInterval(()=>{
					this.codetime--;
					if(this.codetime < 1) {
						clearInterval(timer)
						this.codetime = 0;
					}
				}, 1000);
			}
		}
	}
</script>

<style>
@import "../../common/form.css";

.input-box {
	display: flex;
	position: relative;
	flex-direction: row;
	border-bottom: 1upx solid #F4F4F4;
}

.input-box .text {
	font-size: 30upx;
	margin: 20upx 20upx;
	width:170upx;
}

.input-box .validator {
	font-size: 30upx;
	color:#BBBBBB; 
	width: 150upx;
	position: absolute;
	top: 0;
	right: 0;
	height: 100%;
	z-index: 100;
}

.input-box .validator .validate-text {
	height: 100%;
	display: flex;
	flex-direction: row;
	align-items: center;
	justify-content: center;
	background: #EEEEEE;
	border-radius: 10upx;
	font-size: 25upx;
	width: 150upx;
}
	

.input-box .validate-input {
}

.input-box .text::before {
	position: absolute;
	left: 4px;
	color: #ee0a24;
	font-size: 14px;
	content: '*';
}

.checkbox {
	display: flex;
	flex-direction: row;
	font-size: 30upx;
	color:#BBBBBB; 
	margin: 20upx 10upx;
}

.link {
	text-decoration: underline;
	color:#0A98D5;
}


    
</style>
