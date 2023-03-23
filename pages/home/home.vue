<template>
	<view>
		<!-- 轮播图的区域 -->
		<swiper :options="swiperOptions" :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000"
			:circular="true">
			<swiper-item v-for="(item, i) in swiperList" :key="i">
				<navigator class="swiper-item">
					<image :src="item.image_src"></image>

				</navigator>
			</swiper-item>
		</swiper>
		<van-grid column-num="3" square icon-size="40px">
			<van-grid-item v-for="(item, i) in navList" :key="i" :icon="item.icon" :text="item.text"
				:link-type="item.linkType" :url="item.url" />
		</van-grid>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				swiperOptions: {
					autoHeight: true
				},
				// 这是轮播图的数据列表
				swiperList: [{
						id: "1",
						image_src: "/static/images/gift.png"
					},
					{
						id: "2",
						image_src: "/static/images/ballroom-dance.png"
					}
				],
				// 分类导航的数据列表
				navList: [{
						id: "1",
						icon: "todo-list",
						linkType: "navigateTo",
						url: "/pages/list/list",
						text: "任务"
					},
					{
						id: "2",
						icon: "photo",
						linkType: "navigateTo",
						url: "/pages/picture/picture",
						text: "照片"
					},
					{
						id: "3",
						icon: "video",
						linkType: "navigateTo",
						url: "/pages/videos/videos",
						text: "视频"
					},
					{
						id: "4",
						icon: "http://115.28.105.227:8888/wxapplets/6271659063266_.pic.jpg",
						linkType: "navigateTo",
						url: "/pages/random/random",
						text: "随机"
					},
					{
						id: "5",
						icon: "star",
						linkType: "navigateTo",
						url: "/pages/ai/ai",
						text: "解闷"
					},
					{
						id: "6",
						icon: "star",
						linkType: "navigateTo",
						url: "",
						text: "待定"
					}
				],
				// 楼层的数据
				floorList: []
			};
		},
		onLoad() {
			// 调用方法，获取轮播图的数据
			// this.getSwiperList()
			// this.getNavList()
			// this.getFloorList()
		},
		methods: {

			// async getSwiperList() {
			// 	//       const { data: res } = await uni.$http.get('/api/public/v1/home/swiperdata')
			// 	//       // 请求失败
			// 	//       if (res.meta.status !== 200) return uni.$showMsg()

			// 	//       this.swiperList = res.message
			// },
			// async getNavList() {
			// 	const {
			// 		data: res
			// 	} = await uni.$http.get('/api/public/v1/home/catitems')
			// 	if (res.meta.status !== 200) return uni.$showMsg()
			// 	this.navList = res.message
			// },
			// navClickHandler(item) {
			// 	if (item.name === '分类') {
			// 		uni.switchTab({
			// 			url: '/pages/cate/cate'
			// 		})
			// 	}
			// },
			// // 获取首页楼层数据的方法
			// async getFloorList() {
			// 	const {
			// 		data: res
			// 	} = await uni.$http.get('/api/public/v1/home/floordata')
			// 	if (res.meta.status !== 200) return uni.$showMsg()

			// 	// 对数据进行处理
			// 	res.message.forEach(floor => {
			// 		floor.product_list.forEach(prod => {
			// 			prod.url = '/subpkg/goods_list/goods_list?' + prod.navigator_url.split('?')[1]
			// 		})
			// 	})
			// 	this.floorList = res.message
			// }
		}
	}
</script>


<style lang="scss">
	swiper {
		height: 600rpx;

		.swiper-item,
		image {
			width: 100%;
			height: 100%;
		}
	}
</style>
