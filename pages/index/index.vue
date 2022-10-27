<template>
	<view class="home">
		<scroll-view scroll-x class="navScroll">
			<view :class="index==navIndex?'item active':'item'" v-for="(item,index) in navData"
				@click="clickNav(index,item.id) " :key="item.id">
				{{item.classname}}
			</view>
		</scroll-view>
		<view class="content">
			<view class="row" v-for="item in newData" :key="item.id">
				<newsbox @click.native="goDetail(item)" :item="item"></newsbox>
			</view>
		</view>
		<view class="noData" v-if="!newData.length">
			<image src="../../static/images/nodata.png" mode="widthFix"></image>
		</view>
		<view class="loading" v-if="newData.length">
			<view v-if="loading==1">
				数据加载中....
			</view>
			<view v-if="loading==2">
				-----我也是有底线的呀-----
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				navIndex: 0,
				navData: [],
				newData: [],
				currentPage: 1,
				// 底部 0为默认 1 为加载中 2没有更多了
				loading: 0,
			}
		},
		onLoad() {
			this.getNavData()
			this.getNewData()
		},

		methods: {

			// 切换新闻板块触发
			clickNav(navIndex, id) {

				this.navIndex = navIndex
				// 切换板块的时候 将页面数处置为1 以及之前获取到的数据清空
				this.currentPage = 1
				// 默认
				this.loading = 0
				this.newData = []
				this.getNewData(id)
			},
			// 跳转到新闻详情页
			goDetail(item) {
				console.log(item);
				// 跳转 并携带参数 （新闻id 栏目id）
				uni.navigateTo({
					url: `/pages/detail/detail?id=${item.id}&cid=${item.classid}`
				})
			},

			// 获取导航栏数据
			getNavData() {
				uni.request({
					url: 'https://ku.qingnian8.com/dataApi/news/navlist.php',
					success: (res) => {
						this.navData = res.data
					}
				})
			},

			// 获取新闻列表数据
			getNewData(id) {
				uni.request({
					url: 'https://ku.qingnian8.com/dataApi/news/newslist.php',
					data: {
						num: 10,
						cid: id,
						page: this.currentPage
					},
					success: (res) => {
						if (res.data.length == 0) this.loading = 2
						this.newData = [...this.newData, ...res.data]
						console.log(this.newData);
					}
				})
			}
		},

		// 上拉触底 触发该函数
		onReachBottom() {
			if (this.loading == 2) {
				return
			}
			// 触底加载更多
			this.currentPage++

			// 显示加载中
			this.loading = 1
			// 调用新闻接口
			this.getNewData()
		}

	}
</script>

<style lang="scss" scoped>
	.navScroll {
		height: 100rpx;
		background-color: #F7F8FA;
		white-space: nowrap;
		position: fixed;
		top: var(--window-top);
		left: 0;
		z-index: 10;

		// 去除自带的滚动条 主要去除 h5
		/deep/ ::-webkit-scrollbar {
			width: 4px !important;
			height: 1px !important;
			overflow: auto !important;
			background: transparent !important;
			-webkit-appearance: auto !important;
			display: block;
		}

		.item {
			font-size: 40rpx;
			display: inline-block;
			line-height: 100rpx;
			padding: 0 30rpx;
			color: #333;

			&.active {
				color: #31c27f;
			}
		}
	}

	.content {
		padding: 30rpx;
		padding-top: 130rpx;

		.row {
			border-bottom: 1px dotted #efefef;
			padding: 15rpx 0;
		}
	}

	.noData {}

	.loading {
		text-align: center;
		font-size: 26rpx;
		color: #888;
		line-height: 2em;
	}
</style>
