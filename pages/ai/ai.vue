<template>
	<view>
		<scroll-view scroll-with-animation scroll-y="true" style="width: 100%;">
			<!-- 用来获取消息体高度 -->
			<view id="okk" scroll-with-animation>
				<!-- 消息 -->
				<view class="flex-column-start" v-for="(x,i) in msgList" :key="i">
					<!-- 用户消息 头像可选加入-->
					<view v-if="x.my" class="flex justify-end padding-right one-show  align-start  padding-top">
						<!-- 	<image v-if="!x.my" class="chat-img" src="../../static/..." mode="aspectFill" ></image> -->
						<view class="flex justify-end" style="width: 400rpx;">
							<view class="margin-left padding-chat bg-cyan" style="border-radius: 35rpx;">
								<text style="word-break: break-all;">{{x.msg}}</text>
							</view>
						</view>
						<!-- <image class="chat-img margin-left" src="../../static/..." mode="aspectFill" ></image> -->
					</view>
					<!-- 机器人消息 -->
					<view v-if="!x.my" class="flex-row-start margin-left margin-top one-show">
						<view class="chat-img flex-row-center">
							<image style="height: 75rpx;width: 75rpx;" src="../../static/images/robt.png"
								mode="aspectFit"></image>
						</view>
						<view class="flex" style="width: 500rpx;">
							<view class="margin-left padding-chat flex-column-start"
								style="border-radius: 35rpx;background-color: #f9f9f9;">
								<text style="word-break: break-all;">{{x.msg}}</text>
							</view>
						</view>
					</view>
				</view>
				<!-- loading是显示 -->
				<view v-show="msgLoad" class="flex-row-start margin-left margin-top">
					<view class="chat-img flex-row-center">
						<image style="height: 75rpx;width: 75rpx;" src="../../static/image/robt.png" mode="aspectFit">
						</image>
					</view>
					<view class="flex" style="width: 500rpx;">
						<view class="margin-left padding-chat flex-column-start"
							style="border-radius: 35rpx;background-color: #f9f9f9;">
							<view class="cuIcon-loading turn-load" style="font-size: 35rpx;color: #3e9982;">

							</view>
						</view>
					</view>
				</view>
				<!-- 防止消息底部被遮 -->
				<view style="height: 130rpx;">

				</view>
			</view>

		</scroll-view>

		<!-- 底部导航栏 -->
		<view class="flex-column-center" style="position: fixed;bottom:0px;width: 100%;">
			<view class="bottom-dh-char flex-row-around" style="font-size: 55rpx;">
				<!-- vue无法使用软键盘"发送" -->
				<input v-model="msg" class="dh-input" type="text" style="background-color: #f0f0f0;" @confirm="sendMsg"
					confirm-type="search" placeholder-class="my-neirong-sm" placeholder="用一句简短的话描述您的问题" />
				<button @click="sendMsg" :disabled="msgLoad" class="btn bg-cyan round">发送</button>
			</view>
		</view>
	</view>
</template>

<script>
	const app = getApp();
	const baseUrl = app.globalData.baseUrl;
	export default {
		onLoad() {
			this.userId = new Date().getTime()
		},
		data() {
			return {
				msgLoad: false,
				anData: {},
				userId: "",
				animationData: {},
				showTow: false,
				msgList: [{
					my: false,
					msg: "我是小🐟,请问有什么问题可以帮助您?"
				}],
				msgContent: "",
				msg: ""
			}
		},
		methods: {
			sendMsg() {
				// 消息为空不做任何操作
				if (this.msg == "") {
					return 0;
				}
				this.msgList.push({
					"msg": this.msg,
					"my": true
				})
				this.msgContent = this.msg
				this.msgLoad = true
				// 清除消息
				this.msg = ""
				let self = this
				uni.request({					
					method: "POST",
					url: "http://localhost:8090" + "/talk",
					// url: baseUrl + "/talk",
					data: {
						"prompt": this.msgContent
					},
					header:{
						"content-type": "application/x-www-form-urlencoded"
					},
					success: function(data) {
						console.log(data.data.data)
						let text = data.data.data
							.replaceAll("openai:", "")
							.replaceAll("openai：", "")
							.replaceAll("OpenAi:", "")
							.replaceAll("OpenAi：", "")
							.replaceAll("OpenAI：", "")
							.replaceAll("OpenAI:", "")
							.replaceAll(/^\n|\n$/g, "")
						console.log('text:'+text)
						self.msgList.push({
							"msg": text,
							"my": false
						})
						self.msgContent += ("openai:" + self.msg + "\n")
						self.msgLoad = false
					}
				})
			},
			hideKey() {
				uni.hideKeyboard()
			},
		}
	}
</script>

<style>
	/*每个页面公共css */
	@import "/colorui/main.css";
	@import "/colorui/icon.css";
	@import "/static/css/index-app.css";

	.bottom-dh-char {
		background-color: #f9f9f9;
		width: 100%;
		height: 110rpx;
	}

	.center-box {
		width: 720rpx;
		padding-left: 25rpx;
	}

	.btn {
		height: 90rpx;
		width: 20%;
	}

	.flex-row-start {
		display: flex;
		flex-direction: row;
		align-items: flex-start;
	}

	.hui-box {
		width: 750rpx;
		height: 100%;

	}

	.dh-input {
		width: 75%;
		height: 90rpx;
		border-radius: 100rpx;
		padding-left: 40rpx;
		margin-left: 20rpx;
		background-color: #FFFFFF;
	}

	.box-normal {
		width: 750rpx;
		height: 180px;
		background-color: #FFFFFF;
	}

	.tb-text view {
		font-size: 65rpx;
	}

	.tb-text text {
		font-size: 25rpx;
		color: #737373;
	}

	.chat-img {
		border-radius: 50%;
		width: 100rpx;
		height: 100rpx;
		background-color: #f7f7f7;
	}

	.padding-chat {
		padding: 17rpx 20rpx;
	}

	.tb-nv {
		width: 50rpx;
		height: 50rpx;
	}
</style>
