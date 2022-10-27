<template>
	<view class="mewsbox">
		<view class="pic">
			<image :src="item.picurl" mode="aspectFill"></image>
		</view>
		<view class="text">
			<view class="textTop">
				{{item.title}}
			</view>
			<view class="textBottom" v-if="!item.lookTime">
				<text>{{item.author}}</text>
				<text>{{item.hits}}</text>
			</view>
			<view class="textBottom" v-else>
				<text>浏览时间：{{item.lookTime}}</text>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		name: "newsbox",
		props:{
			item: {
				type: Object,
				default(){
					return {
						title: '默认新闻标题默认新闻标题默认新闻标题默认新闻标题认新闻标题',
						author:'张三',
						hits:666,
						picurl:'../../static/images/nopic.jpg',
						lookTime:'2022-12-12 10:10:10'
					}
				}
			}
		},
		data() {
			return {
				
			};
		}
	}
</script>

<style lang="scss">
	.mewsbox {
		display: flex;

		.pic {
			width: 230rpx;
			height: 160rpx;

			image {
				width: 100%;
				height: 100%;
			}
		}

		.text {
			flex: 1;
			display: flex;
			flex-wrap: wrap;
			align-content: space-between;
			padding-left: 20rpx;

			.textTop {
				font-size: 36rpx;
				// 多行省略
				display: -webkit-box;
				overflow: hidden;
				white-space: normal !important;
				text-overflow: ellipsis;
				word-wrap: break-word;
				-webkit-line-clamp: 2;
				-webkit-box-orient: vertical;
			}

			.textBottom {
				font-size: 26rpx;
				color: #999;

				text {
					padding-right: 30rpx;
				}
			}
		}
	}
</style>
