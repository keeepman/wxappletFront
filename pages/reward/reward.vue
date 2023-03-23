<template>
	<view>
		<van-card
		  tag="new"
		  price="100积分"
		  desc="最终解释权归受气方所有"
		  title="小事原谅卡"
		  thumb="http://115.28.105.227:8888/wxapplets/WechatIMG657.png"
		  currency=" ">
		  <view slot="footer">
		    <van-button size="mini">兑换</van-button>
		  </view>
		</van-card>
		<van-card
		  tag="new"
		  price="(礼物价格/10)积分"
		  desc="想要礼物就要多做任务"
		  title="礼物券"
		  thumb="http://115.28.105.227:8888/wxapplets/WechatIMG656.png"
		  currency=" ">
		  <view slot="footer">
		    <van-button size="mini">兑换</van-button>
		  </view>
		</van-card>

	</view>
</template>

<script>
	export default {
		data() {
			return {
				
			};
		}
	}
</script>

<style lang="scss">

</style>
