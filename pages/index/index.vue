<template>
	<view class="home">
		<!-- 横向可移动分类导航栏  scroll-x 表示横向可以移动-->
		<scroll-view scroll-x class="navScroll">
			<view :class="index == navIndex ? 'item active' : 'item'" v-for="(item, index) in navData" @click="clickNav(index, item.id)" :key="item.id">{{ item.classname }}</view>
		</scroll-view>
		<!-- 主内容 -->
		<view class="content">
			<view class="row" v-for="item in newData" :key="item.id">
				<!-- newsbox 为 components中定义的组件 在uniapp可以直接使用 但一定要在指定目录下 -->
				<newsbox @click.native="goDetail(item)" :item="item"></newsbox>
			</view>
		</view>
		
		<!-- 三种展示效果 一条数据都没有时、数据在加载时、没有更多数据时 -->
		<view class="noData" v-if="!newData.length">
			<image src="../../static/images/nodata.png" mode="widthFix"></image>
			<view class="text">小编正在奋笔疾书</view>
		</view>
		<view class="loading" v-if="newData.length">
			<view v-if="loading == 1">数据加载中....</view>
			<view v-if="loading == 2">-----我也是有底线的呀-----</view>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			navIndex: 0,
			// navData 顶部菜单 newData新闻数据
			navData: [],
			newData: [],
			currentPage: 1,
			// 底部 0为默认 1 为加载中 2没有更多了
			loading: 0
		};
	},
	// 页面加载时触发  一个页面只会调用一次
	onLoad() {
		this.getNavData();
		this.getNewData();
	},

	methods: {
		// 获取导航栏数据
		getNavData() {
			uni.request({
				url: 'https://ku.qingnian8.com/dataApi/news/navlist.php',
				success: res => {
					this.navData = res.data;
				}
			});
		},

		// 切换新闻板块触发
		clickNav(navIndex, id) {
			// navIndex 为当前被选中板块 index ，id是当前板块实际数据库id
			this.navIndex = navIndex;
			// 切换板块的时候 将page数处置为1(分页)， 以及之前获取到的数据清空
			this.currentPage = 1;
			// 默认
			this.loading = 0;
			this.newData = [];

			// 将点击的 板块类型 id 传递给其他函数处理
			this.getNewData(id);
		},

		// 点击当前新闻 跳转到新闻详情页
		goDetail(item) {
			console.log(item);
			// 跳转 并携带参数 （新闻id 栏目id）
			uni.navigateTo({
				url: `/pages/detail/detail?id=${item.id}&cid=${item.classid}`
			});
		},

		// 通过点击类型 获取新闻列表数据 初次执行的时候 使用默认参数
		getNewData(id) {
			// 发送axios请求
			uni.request({
				url: 'https://ku.qingnian8.com/dataApi/news/newslist.php',
				data: {
					num: 10,
					cid: id,
					page: this.currentPage
				},
				success: res => {
					// 请求成功并得到响应 判断当前次的响应 data的长度 等于0就是没有参数 显示没有数据了
					if (res.data.length == 0) this.loading = 2;
					// 因为下拉加载 会有多次请求 将多次请求得到的数据 进行拼接存入一个变量中
					this.newData = [...this.newData, ...res.data];
					console.log(this.newData);
				}
			});
		}
	},

	// 上拉触底 触发该函数
	onReachBottom() {
		// 触底后得到了响应 如果 loading == 2 说明响应的数据 是空的 直接退出不在发送请求了
		if (this.loading == 2) {
			return;
		}
		// 触底加载更多 主要是通过传递分页参数
		this.currentPage++;

		// 显示加载中
		this.loading = 1;
		// 调用新闻接口
		this.getNewData();
	}
};
</script>

<style lang="scss" scoped>
.navScroll {
	height: 100rpx;
	background-color: #f7f8fa;
	white-space: nowrap;
	position: fixed;
	top: var(--window-top);
	left: 0;
	z-index: 10;

	// 去除自带的滚动条 主要去除 h5  scroll-view标签
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

.noData {
	text-align: center;

	image {
		width: 350rpx;
	}

	.text {
		color: #888;
		font-size: 30rpx;
	}
}

.loading {
	text-align: center;
	font-size: 26rpx;
	color: #888;
	line-height: 2em;
}
</style>
