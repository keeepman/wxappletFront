<template>
	<view>
		<van-field clearable label="标题" left-icon="font" placeholder="请输入标题内容" :value="title"
			@change="title = $event.mp.detail" />
		<van-field type="textarea" clearable left-icon="comment" label="描述" placeholder="请输入描述内容" :value="describe"
			@change="describe = $event.mp.detail" autosize />
		<van-cell title="地点" :value="address" @click="chooseAddr" />
		<van-cell title="时间" :value="date" @click="onDisplay" />
		<van-calendar :show="show" :min-date="minDate" @close="onClose" @confirm="onConfirm" :show-confirm="false" />
		<van-divider contentPosition="center">上传资源</van-divider>
		<van-uploader :file-list="previewList" accept="media" multiple="true" @after-read="previewImg" max-count="9"
			@delete="deleteImg" />
		<van-button type="danger" @click="submit" block round icon="good-job">上传</van-button>

	</view>
</template>

<script>
	const app = getApp();
	const baseUrl = app.globalData.baseUrl;
	export default {
		data() {
			return {
				minDate: new Date(2022, 0, 1).getTime(),
				describe: '',
				title: '',
				date: '',
				address: '',
				addressDetail: '',
				fileList: [],
				previewList: [],
				file: null,
				show: false,
				typeId: Number
			};
		},
		methods: {
			onLoad(options) {
				this.typeId = options.typeId
			},
			chooseAddr() {
				this.getAuthorizeInfo();
			},
			// 位置授权
			getAuthorizeInfo() {
				const that = this;
				uni.authorize({
					scope: 'scope.userLocation',
					success() {
						// 允许授权
						that.getLocationInfo();
					},
					fail() {
						// 拒绝授权
						that.openConfirm();
						console.log("你拒绝了授权，无法获得周边信息")
					}
				})
			},
			// 获取地理位置
			getLocationInfo() {
				const that = this;
				uni.getLocation({
					type: 'wgs84',
					success(res) {
						uni.chooseLocation({
							latitude: res.latitude,
							longitude: res.longitude,
							success: function(data) {
								that.address = data.name
								that.addressDetail = data.address
							}
						})
					}
				});
			},
			// 再次获取授权。当用户第一次拒绝后再次请求授权
			openConfirm() {
				uni.showModal({
					title: '请求授权当前位置',
					content: '需要获取您的地理位置，请确认授权',
					success: (res) => {
						if (res.confirm) {
							uni.openSetting(); // 打开地图权限设置
						} else if (res.cancel) {
							uni.showToast({
								title: '你拒绝了授权，无法获得周边信息',
								icon: 'none',
								duration: 1000
							})
						}
					}
				});
			},
			onDisplay() {
				this.show = true
			},
			onClose() {
				this.show = false
			},
			formatDate(date) {
				date = new Date(date);
				return `${date.getYear()+1900}/${date.getMonth() + 1}/${date.getDate()}`;
			},
			onConfirm(event) {
				this.show = false,
					console.log(event)
				this.date = this.formatDate(event.detail)
			},
			previewImg(e) {
				const {
					previewFile
				} = e.detail
				const filrList = e.detail.file
				console.log(filrList)
				for (const key in filrList) {
					const element = filrList[key];
					this.previewList.push({
						// 相当于将previewFile所有的属性都放入
						...previewFile,
						url: element.url
					})
				}
			},
			deleteImg(event) {
				console.log(event)
				const {
					index,
					name
				} = event.detail;
				this.previewList.splice(index, 1);
			},
			submit() {
				uni.showLoading({
					title: "开始上传..."
				})
				let self = this
				let length = self.previewList.length
				if (length !== 0) {
					uni.request({
						method: "POST",
						url: baseUrl + "/saveBasicInformation",
						data: {
							"title": self.title,
							"describe": self.describe,
							"date": self.date,
							"address": self.address,
							"addressDetail": self.addressDetail,
							"typeId": self.typeId
						},
						header:{
							"token": uni.getStorageSync('token')
						},
						success: function(data) {
							for (let i = 0; i < length; i++) {
								uni.uploadFile({
									filePath: self.previewList[i].url,
									url: baseUrl + "/uploadFile",
									name: "previewList",
									formData: {
										"previewList": self.previewList[i].url,
										"mediaBaseInfoId": data.data.data
									},
									header: {
										"content-type": "multipart/form-data",
										"token": uni.getStorageSync('token')
									},
									success: function() {
										uni.showToast({
											title: '上传成功',
											success: function() {
												setTimeout(function () {
													uni.navigateBack();
												}, 1500);
											}
										})
									},
									fail: function() {
										uni.showToast({
											title: '上传失败'
										})
									}
								})
							}
						}
					})
				}
			}
		}
	}
</script>

<style lang="scss">

</style>
