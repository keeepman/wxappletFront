<template>
	<view class="event-list">
		<view v-for="(item,index) in videoList" :key="index">
			<view class="event-link">
				<view class="event-img" v-for="(itm,inm) in item.mediaUrls" :key="inm">
					<video :src="itm.url"></video>
					<van-icon class="event-download" size="20px" name="down" @click="saveVideo(itm.url)" />
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
				videoList: [],
				page: 1
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
				this.getVideoList()
			}
		},
		methods: {
			//上拉刷新事件
			onReachBottom() {
				this.page++;
				this.getVideoList();
			},
			getVideoList() {
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
						"mediaType": 1
					},
					header: {
						"token": uni.getStorageSync('token')
					},
					success: function(data) {
						uni.hideLoading();
						self.videoList.push(...data.data.data)
					}
				})
			},
			saveVideo(url) {
				Dialog.confirm({
						title: 'save',
						message: '是否需要保存视频到本地',
					})
					.then(() => {
						uni.showLoading({
							title: '正在保存视频...'
						});
						uni.downloadFile({
							url: url,
							success: function(data) {
								if (data.statusCode === 200) {
									//文件保存到本地
									uni.saveVideoToPhotosAlbum({
										filePath: data.tempFilePath, //临时路径
										success: function(res) {
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
								}
							},
							fail: function() {
								console.log('失败')
							},
							complete: function() {
								uni.hideLoading();
							}
						})
					})
					.catch(() => {
						// on cancel
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


	.event-img video {
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
	}
</style>
