<template>
	<view class="content">
		
		<view class="text-area">
			<text class="title">{{title}}</text>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				title: '我的记录'
			}
		},
		onLoad() {

		},
		methods: {

		}
	}
</script>

<style>
	.content {
		height: 100%;
		padding: 15px;
		background-color: azure;
	}
	.title {
		font-size: 36rpx;
		color: #8f8f94;
	}
</style>
