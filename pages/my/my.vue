<template>
	<view class="my-container">
		<mylogin v-if="!token"></mylogin>
		<view v-else>
			<myuserinfo v-if="forceRefresh"></myuserinfo>
		</view>
	</view>
</template>

<script>
	import mylogin from '@/wxcomponents/my-login/my-login'
	import myuserinfo from '@/wxcomponents/my-userinfo/my-userinfo'
	import {
		mapState
	} from 'vuex'

	export default {
		components: {
			mylogin,
			myuserinfo
		},
		data() {
			return {
				forceRefresh: Boolean
			};
		},
		computed: {
			...mapState('m_user', ['token'])
		},
		onShow() {
			this.forceRefresh = false;
			this.$nextTick(() => {
				this.forceRefresh = true;
			})
		}
	}
</script>

<style lang="scss">
	page,
	.my-container {
		height: 100%;
	}
</style>
