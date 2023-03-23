<template>
	<view class="login-container">
		<uni-icons type="contact-filled" size="100" color="#AFAFAF"></uni-icons>
		<button type="primary" class="btn-login" @click="getUserProfile">一键登录</button>
		<text class="tips-text">请登录</text>
	</view>
</template>

<script>
	const app = getApp();
	const baseUrl = app.globalData.baseUrl;
	import {
		mapMutations,
		mapState
	} from 'vuex'

	export default {
		name: 'my-login',
		data() {
			return {

			};
		},
		computed: {
 			...mapState('m_user', ['redirectInfo'])
		},
		methods: {
			...mapMutations('m_user', ['updateUserInfo', 'updateToken', 'updateRedirectInfo']),
			// 获取微信用户的基本信息
			getUserProfile() {
				uni.getUserProfile({
					desc: '你的授权信息',
					success: (res) => {
						console.log(res)
						// 将信息存到 vuex 中
						this.updateUserInfo(res.userInfo)
						this.getToken(res)
					},
					fail: (res) => {
						return uni.$showMsg('您取消了登录授权')
					}
				})
			},
			// 用户授权之后，获取用户的基本信息
			// getUserInfo(e) {
			//   console.log(e)

			//   if (e.detail.errMsg === 'getUserInfo:fail auth deny') return uni.$showMsg('您取消了登录授权！')

			//   console.log(e.detail.userInfo)
			//   this.updateUserInfo(e.detail.userInfo)

			//   this.getToken(e.detail)
			// },
			async getToken(info) {
				let self = this
				// 获取 code 对应的值
				const [err, res] = await uni.login().catch(err => err)

				if (err || res.errMsg !== 'login:ok') return uni.$showMsg('登录失败！')

				// 准备参数
				const query = {
				 	code: res.code,
					encryptedData: info.encryptedData,
					iv: info.iv,
					rawData: info.rawData,
					signature: info.signature
				}
				await uni.request({
					method: "POST",
					data: query,
					url: baseUrl + "/login",
					success: function(msg) {
						console.log(msg)
						if (msg.data.resultCode !== 2000) return uni.$showMsg('登录失败！')
						self.updateToken(msg.data.data)
						self.navigateBack()
					}
				})
			},
			navigateBack() {
				let self = this
				// if (self.redirectInfo && self.redirectInfo.openType === 'switchTab') {
				// 	uni.switchTab({
				// 		url: self.redirectInfo.from,
				// 		complete: () => {
				// 			self.updateRedirectInfo(null)
				// 		}
				// 	})
				// }
				uni.navigateBack()
			}
		}
	}
</script>

<style lang="scss">
	.login-container {
		height: 750rpx;
		background-color: #F8F8F8;
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		position: relative;
		overflow: hidden;

		&::after {
			content: ' ';
			display: block;
			width: 100%;
			height: 40px;
			background-color: white;
			position: absolute;
			bottom: 0;
			left: 0;
			border-radius: 100%;
			transform: translateY(50%);
		}

		.btn-login {
			width: 90%;
			border-radius: 100px;
			margin: 15px 0;
			background-color: #C00000;
		}

		.tips-text {
			font-size: 12px;
			color: gray;
		}
	}
</style>
