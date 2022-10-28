<template>
	<!-- 新闻详情页 -->
	<view class="detail">
		<view class="title">{{ detailData.title }}</view>
		<view class="info">
			<view class="author">编辑：{{ detailData.author }}</view>
			<view class="time">发布日期：{{ detailData.posttime }}</view>
		</view>
		<view class="center">
			<!-- 富文本解析 rich-text是uni内置的解析富文本标签  -->
			<rich-text :nodes="detailData.content"></rich-text>
		</view>
		<view class="description">仅供学习</view>
	</view>
</template>

<script>
// 引入 封装好的时间处理 函数
import { parseTime } from '@/utils/tool.js';
export default {
	data() {
		return {
			options: '',
			detailData: {}
		};
	},
	// 获取 uni.navigateTo（）路由传递的参数
	// 页面加载时触发，一个页面只会触发一次 可以获取到前页面的路径信息 param
	onLoad(e) {
		console.log(e);
		this.options = e;
		this.getDetail();
	},
	methods: {
		// 获取新闻详情数据
		getDetail() {
			uni.request({
				url: 'https://ku.qingnian8.com/dataApi/news/detail.php',
				// onLoad 获取到的 cid 和id
				data: this.options,
				success: res => {
					// 发送请求并成功响应
					console.log(res);
					// 为了兼容 富文本中的图片 在微信小程序中完美展示 使用查找替换给图片设置了样式
					res.data.content = res.data.content.replace(/<img/gi, '<img style="max-width:100%"');
					// 处理时间格式
					res.data.posttime = parseTime(res.data.posttime, '{y}-{m}-{d} {h}:{i}:{s}');
					// 将获取到的参数全部处理好后  进行赋值
					this.detailData = res.data;
					
					// 动态设置当前页面的标题 标题使用新闻标题
					uni.setNavigationBarTitle({
						title: this.detailData.title
					});

					// 调用浏览历史记录函数
					this.newsHistory();
				}
			});
		},

		// 浏览历史记录
		newsHistory() {
			// 判断浏览器中的缓存是否存在
			let historyArr = uni.getStorageSync('historyArr') || [];
			// 将当前页的重要参数 进行保存起来
			let item = {
				id: this.detailData.id,
				classid: this.detailData.classid,
				picurl: this.detailData.picurl,
				lookTime: parseTime(Date.now()),
				title: this.detailData.title
			};
			// 浏览记录去重
			let index = historyArr.findIndex(i => {
				return i.id == this.detailData.id;
			});
			if (index >= 0) {
				historyArr.splice(index, 1);
			}
			console.log(index);
			// 添加到当前数组中 setStorageSync会覆盖所以采用数组形式
			historyArr.unshift(item);
			// 只展示 10条浏览数据
			historyArr = historyArr.splice(0, 10);
			// setStorageSync 同步存储
			uni.setStorageSync('historyArr', historyArr);
		}
	}
};
</script>

<style lang="scss" scoped>
.detail {
	padding: 30rpx;

	.title {
		font-size: 46rpx;
		color: #333;
	}

	.info {
		background: #f6f6f6;
		padding: 20rpx 10rpx;
		font-size: 25rpx;
		color: #666;
		display: flex;
		justify-content: space-between;
		margin: 40rpx 0;
	}
}

.description {
	margin-top: 30rpx;
	background: #fef0f0;
	font-size: 26rpx;
	padding: 20rpx;
	color: red;
	line-height: 1.8em;
}

.center {
	padding-bottom: 50rpx;
	// 针对富文本中的图片 进行设置
	// 当前穿透设置 只对 h5 有效 微信小程序中不兼容
	// /deep/ img{
	// 	max-width: 100%;
	// }
}
</style>
