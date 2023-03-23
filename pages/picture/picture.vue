<template>
	<view class="event-list">
		<!-- 循环 -->
		<view v-for="(item,index) in imgList" :key="index">
			<view class="event-link">
				<view class="event-img">
					<van-grid :column-num="item.mediaUrls.length>=3?3:item.mediaUrls.length" :border="false">
						<van-grid-item v-for="(itm,inm) in item.mediaUrls" :key="inm" use-slot>
							<image v-show="item.mediaUrls.length<2" mode="widthFix" style="width: 100%;" :src="itm.url"
								@click="imgClick(itm.url,item.mediaUrls)"></image>
							<image v-show="item.mediaUrls.length>=2" mode="aspectFill"
								style="width: 100%; height: 200rpx;" :src="itm.url"
								@click="imgClick(itm.url,item.mediaUrls)"></image>
						</van-grid-item>
					</van-grid>

				</view>
				<view class="event-content">
					<view class="event-title">
						<van-icon name="fire" />{{item.title}}
					</view>
					<view class="event-desc">
						<van-icon name="comment-circle" />{{item.describe}}
					</view>
					<view class="event-box">
						<view class="event-address">
							<van-icon name="map-marked" />{{item.address+' | '}}{{item.addressDetail}}
						</view>
						<view class="event-time">
							<van-icon name="underway" />{{item.date}}
						</view>
					</view>
				</view>
			</view>
		</view>
		<!-- 循环 -->
		<van-dialog id="van-dialog" />
	</view>
</template>

<script>
	import Dialog from '../../wxcomponents/vant/dist/dialog/dialog';
	const app = getApp();
	const baseUrl = app.globalData.baseUrl;
	import {
		mapState
	} from 'vuex'

	export default {
		data() {
			return {
				imgList: [],
				page:1
			}
		},
		computed: {
			...mapState('m_user', ['token'])
		},
		onLoad() {
			if (!this.token) {
				uni.showLoading({
					title: '请先登陆'
				});
				setTimeout(function() {
					uni.switchTab({
						url: '/pages/my/my'
					})
				}, 500);
			} else {
				this.getImgList()
			}
		},
		methods: {
			//上拉刷新事件
			onReachBottom() {
				if (this.status == 'nomore') {
					return
				}
				this.page++;
				this.getImgList();
			},
			getImgList() {
				uni.showLoading({
					title: "加载中……"
				});
				let self = this
				uni.request({
					method: "GET",
					url: baseUrl + "/showMedia",
					data: {
						"page": self.page,
						"pageSize": 10,
						"mediaType": 0
					},
					header: {
						"token": uni.getStorageSync('token')
					},
					success: function(data) {
						uni.hideLoading();
						self.imgList.push(...data.data.data)
					}
				})
			},
			imgClick(currenturl, mediaUrls) {
				var imgSrcList = new Array();
				for (let index in mediaUrls) {
					imgSrcList.push(mediaUrls[index].url)
				}
				uni.previewImage({
					current: currenturl,
					urls: imgSrcList
				})
			},
			saveImage(url) {
				Dialog.confirm({
						title: 'save',
						message: '是否需要保存照片到本地',
					})
					.then(() => {
						uni.showLoading({
							title: '正在保存图片...'
						});
						uni.getImageInfo({
							src: url,
							success: function(res) {
								const url = res.path
								uni.saveImageToPhotosAlbum({
									filePath: url,
									success: function() {
										uni.showToast({
											icon: 'none',
											mask: true,
											title: '文件已保存', //保存路径
											duration: 3000,
										})
									},
									fail: function() {
										uni.showToast({
											icon: 'none',
											mask: true,
											title: '文件保存失败', //保存路径
											duration: 3000,
										})
									}
								});
							},
							complete: function() {
								uni.hideLoading();
							}
						})
					})
					.catch(() => {

					});
			},
		}
	}
</script>


<style>
	.event-list {
		background-color: #fafbfc;
		padding: 20px 0;
	}

	.event-link {
		margin: 10px;
		border-radius: 5px;
		background-color: #fff;
		box-shadow: 5rpx 8rpx 10rpx rgba(53, 178, 225, 0.26);
		overflow: hidden;
	}

	.event-img image {
		width: 100%;
	}

	.event-content {
		padding: 25rpx;
	}

	.event-title {
		line-height: 1.7em;
	}

	.event-desc {
		font-size: 14px;
		color: #666;
		line-height: 1.5em;
		font-weight: 200;
	}

	.event-box {
		margin-top: 15px;
		overflow: hidden;
	}

	.event-address,
	.event-time {
		float: left;
		color: #cecece;
		font-size: 12px;
		padding-right: 15px;
	}

	.event-download {
		float: right;
		color: #cecece;
		font-size: 18px;
		padding-right: 10px;
	}
</style>
