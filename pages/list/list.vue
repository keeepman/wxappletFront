<template>
	<view>
		<van-cell v-for="(item, i) in taskList" :key="i" :title="[item.name+' (+'+item.score+')']"
			:url="splicingUrl(item.id)" value="去完成" :icon="item.iconUrl" is-link />
	</view>
</template>

<script>
	const app = getApp();
	const baseUrl = app.globalData.baseUrl;
	import {
		mapState,mapMutations
	} from 'vuex'

	export default {
		data() {
			return {
				taskList: []
			};
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
				this.getList()
			}
		},
		methods: {
			getList() {
				let self = this
				uni.request({
					method: "GET",
					url: baseUrl + "/showList",
					header: {
						"token": uni.getStorageSync('token')
					},
					success: function(data) {
						self.taskList = data.data.data
					}
				})
			},
			splicingUrl(id) {
				return "/pages/uploader/uploader?typeId=" + id
			}

		}
	}
</script>

<style lang="scss">

</style>
