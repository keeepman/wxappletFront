<template>
	<view>
		<view class="container">
			<h1 class="general title">吃</h1><b id="what">{{eatwhat}}</b>
			<van-button type="warning" @click="eatsubmit" round>看天命</van-button>
		</view>
		<van-divider dashed />
		<view class="container">
			<h1 class="general title">玩</h1><b id="what">{{playwhat}}</b>
			<van-button type="warning" @click="playsubmit" round>看天命</van-button>
		</view>
		<van-divider dashed />
		<view class="container">
			<h1 class="general title">去</h1><b id="what">{{gowhere}}</b>
			<van-button type="warning" @click="gosubmit" round>看天命</van-button>
		</view>
	</view>
</template>

<script>
	var audio;
	export default {
		data() {
			return {
				eatwhat: '什么',
				eatTime: 0,
				playwhat: '什么',
				playTime: 0,
				gowhere: '哪儿',
				goTime: 0,
				foodList: [
					'烤肉', '火锅', '麻辣烫', '面食', '日料', '韩料', '烤鱼', '自助', '炒菜', '茶点', '炸鸡'
				],
				playList: [
					'逛街', '游乐场', '看电影', '按摩', '看脱口秀', '漂流', '密室', '电玩', '爬山'
				],
				goList: [
					'长沙', '重庆', '广州'
				]
			};
		},
		onLoad: function(options) {
			var _this = this;
			audio = uni.createInnerAudioContext();
			audio.onPlay(() => {
				console.info('开始播放');
			});
			//监听播放过程，进度处理
			audio.onTimeUpdate(() => {});
			//播放结束
			audio.onStop(() => {
				console.info('播放结束');
			});
			//播放暂停
			audio.onPause(() => {
				console.info('播放暂停');
			});
			audio.src = 'http://115.28.105.227:8888/wxapplets/20220729_153926.m4a';
		},
		methods: {
			eatsubmit() {
				var self = this;
				self.eatTime = self.eatTime + 1
				if (self.eatTime > 3) {
					self.playClick()
				}
				var r = Math.ceil(Math.random() * self.foodList.length)
				self.eatwhat = self.foodList[r - 1];
			},
			playsubmit() {
				var self = this;
				self.playTime = self.playTime + 1
				if (self.playTime > 3) {
					self.playClick()
				}
				var r = Math.ceil(Math.random() * self.playList.length)
				self.playwhat = self.playList[r - 1];
			},
			gosubmit() {
				var self = this;
				self.goTime = self.goTime + 1
				if (self.goTime > 3) {
					self.playClick()
				}
				var r = Math.ceil(Math.random() * self.goList.length)
				self.gowhere = self.goList[r - 1];
			},
			//播放操作
			playClick: function() {
				audio.autoplay = true;
				//判断是否是播放中
				if (audio.paused) {
					audio.play(); //播放
				} else {
					audio.pause(); //暂停
				}
			},
			//关闭
			stopClick: function() {
				audio.stop();
			},
		}
	}
</script>

<style lang="scss">
	html,
	body,
	template {
		margin: 0;
		padding: 0;
		// background-image: url(./bg.jpg);
		text-align: center;
		height: 100%;
		overflow: hidden;

	}

	#what {
		font-size: 2em;
	}

	.container {
		margin-right: auto;
		margin-left: auto;
	}

	.general {
		text-align: center;
	}

	.title {
		margin-top: 10%;
	}

	.label {
		margin-top: 1%;
	}

	#start:hover {
		background-position: 0 50%;
	}

	#start {
		width: 194px;
		height: 73px;
		// background-image: url(./btn2x.png);
		-webkit-background-size: 582px auto;
		background-size: 582px auto;
		background-color: rgba(255, 0, 0, 0);
		border: none 0;
		outline: none;
		font-size: 18px;
		color: #fff;
		text-shadow: #9e9c9c 0 1px 0;
		cursor: pointer;
		margin-top: 3%;
	}

	.temp {
		position: absolute;
		z-index: 999;
		color: #777;
	}
</style>
