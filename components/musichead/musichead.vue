<template>
	<!-- <view class="music-head" :style="{ color : color }">
		<view class="music-head-icon" :class="{ 'music-head-iconBlack' : iconBlack }">
			<text @tap="handleToBack" class="iconfont iconzuojiantou-copy"></text> | <text @tap="handleToHome"
				class="iconfont iconshouye"></text>
		</view>
		{{ title }}
	</view> -->
	<!-- <view class="music-head-icon" :class="{ 'music-head-iconBlack' : iconBlack }"></view> -->
	<text @tap="handleToBack" class="iconfont iconzuojiantou-copy music-head"></text>
</template>

<script>
	import '../../common/iconfont.css'
	export default {
		data() {
			return {

			};
		},
		props: ['title', 'icon', 'color', 'iconBlack'],
		methods: {
			handleToBack() {
				uni.navigateBack();
			},
			handleToHome() {
				uni.navigateTo({
					url: '/pages/index/index'
				});
			}
		}
	}
</script>

<style>
	.music-head {
		height: 75px;
		width: 75px;
		text-align: center;
		line-height: 80px;
		font-size: 16px;
		color: black;
		overflow: hidden;
		position: relative;
	}

	.music-head-icon {
		width: 97px;
		width: 50px;
		height: 31px;
		background: rgba(0, 0, 0, 0.4);
		border-radius: 18px;
		border: hidden;
		position: absolute;
		left: 20px;
		top: 25px;
		line-height: 31px;
		display: flex;
		border-style: none;
		justify-content: space-evenly;
		color: white;
		/* border-style:none */
	}

	.music-head-iconBlack {
		color: black;
		background: white;
		border: 1px black solid;
	}
</style>
