<template>
	<view class="user">
		
		<view class="top">
			
			<image src="../../static/images/history.png" mode=""></image>
			<view class="text">
				浏览历史
			</view>
		</view>
		
		<!-- 有数据 则渲染数据 将数据传递给 封装的组件 -->
		<view class="content">
			<view class="row" v-for="item in historyData">
				<newsbox :item="item" @click.native="goDetail(item)"></newsbox>
			</view>
		</view>
		<!-- 没有数据 数据长度为0 时显示没有数据的效果 -->
		<view class="noHistory" v-if="!historyData.length">
			<image src="../../static/images/nohis.png" mode="widthFix"></image>
			<view class="text">
				暂无浏览记录
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				historyData: []
			}
		},
		// 应用启动 或从后台既进入前台显示时触发
		// 不使用onShow 需要手动刷新后才会看到最新的数据
		onShow() {
			this.getHistory()
		},
		methods: {
			// 跳转到新闻详情页
			goDetail(item) {
				// 跳转 并携带参数 （新闻id 栏目id）
				uni.navigateTo({
					url: `/pages/detail/detail?id=${item.id}&cid=${item.classid}`
				})
			},
			getHistory() {
				// 从浏览器中获取保存的数据 如果不存在就把 list赋值成数组
				let list = uni.getStorageSync("historyArr") || []
				this.historyData = list
				console.log(this.historyData);
			}
		}
	}
</script>

<style lang="scss" scoped>
	.top {
		display: flex;
		flex-direction: column;
		align-items: center;
		padding: 50rpx 0;
		background-color: #F8F8F8;
		color: #555;

		image {
			width: 150rpx;
			height: 150rpx;
		}

		.text {
			font-size: 38rpx;
			padding-top: 20rpx;
		}
	}

	.content {
		padding: 30rpx;

		.row {
			border-bottom: 1px dotted #efefef;
			padding: 15rpx 0;
		}
	}
	.noHistory{
		display: flex;
		flex-direction: column;
		align-items: center;
		image{
			width: 400rpx;
		}
		.text{
		font-size: 26rpx;	
		color: #888;
		}
	}
</style>
