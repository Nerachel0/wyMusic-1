<template>
	<view class="search">
		<view>
			<view class="box">
				<musichead></musichead>
				<view class="search-search">
					<text class="iconfont iconsearch box"></text>
					<input class="" type="text" placeholder="搜索歌曲" v-model="searchWord" @confirm="handleToSearch "
						@input="handleToSuggest" />
					<text v-show="searchType == 2" @tap="handleToClose" class="iconfont iconguanbi"></text>
				</view>
			</view>
		</view>
		<view class="container" style="">
			<scroll-view scroll-y="true">
				<block v-if="searchType == 1">
					<view class="search-history">
						<view class="search-history-head">
							<text>历史</text>
							<text class="iconfont iconlajitong" @tap="handleToClear"></text>
						</view>
						<view class="search-history-list">
							<view class="tb-js-yf-style" v-for="(item,index) in historyList" :key="index"
								@tap="handleToWord(item)">{{ item }}
							</view>
						</view>
					</view>
					<!-- <view class="box"> -->
						<!-- <view style="background-color: #3e6694;">11</view> -->
						<!-- <view style="background-color: #537caa;">22</view> -->
					<!-- </view> -->
					<view class="search-hot">
						<view class="search-hot-title tb">热搜榜</view>
						<view class="box">
							<view class="music-list-view">
								<view  v-if="index <= 9" class="search-hot-item" style="font-size: 10px;" v-for="(item,index) in searchHot"
									:key="index" @tap="handleToWord(item.searchWord)">
									<view  class="search-hot-top" style="font-size: 8px;">{{ index + 1 }}</view>
									<view class="search-hot-word">
										<view style="font-size: 8px;">
											{{ item.searchWord }}
											<image :src="item.iconType ? item.iconUrl : ''" mode="aspectFit"></image>
										</view>
									</view>
									<text class="search-hot-count">{{ item.score }}</text>
								</view>
							</view>
							<view class="music-list-view">
								<view v-if="index >= 10" class="search-hot-item" style="font-size: 10px;" v-for="(item,index) in searchHot"
									:key="index" @tap="handleToWord(item.searchWord)">
									<view  class="search-hot-top" style="font-size: 8px;">{{ index + 1 }}</view>
									<view  class="search-hot-word">
										<view style="font-size: 8px;">
											{{ item.searchWord }}
											<image :src="item.iconType ? item.iconUrl : ''" mode="aspectFit"></image>
										</view>
										<!-- <view>{{ item.content }}</view> -->
									</view>
									<text  class="search-hot-count">{{ item.score }}</text>
								</view>
							</view>
						</view>
						<!-- <view style="background-color:#bebebe">121212121</view> -->
					</view>
				</block>
				<block v-else-if="searchType == 2">
					<view class="search-result ">
						<view class="search-result-item" v-for="(item,index) in searchList" :key="index"
							@tap="handleToDetail(item.id)">
							<view class="search-result-word">
								<view>{{ item.name }}</view>
								<view>{{ item.artists[0].name }} - {{ item.album.name }}</view>
							</view>
							<text class="iconfont iconbofang"></text>
						</view>
					</view>
				</block>
				<block v-else-if="searchType == 3">
					<view class="search-suggest">
						<view class="search-suggest-title">搜索"{{ this.searchWord }}"</view>
						<view class="search-suggest-item" v-for="(item,index) in suggestList" :key="index"
							@tap="handleToWord(item.keyword)">
							<text class="iconfont iconsearch"></text>
							{{ item.keyword }}
						</view>
					</view>
				</block>
			</scroll-view>
		</view>
	</view>
</template>

<script>
	import {
		searchHot,
		searchWord,
		searchSuggest
	} from '../../common/api.js'
	import '../../common/iconfont.css'
	// import '../../pages/plugins/scroll.vue'
	export default {
		data() {
			return {
				searchHot: [],
				searchWord: '',
				historyList: [],
				searchType: 1,
				searchList: [],
				suggestList: [],
				scrollTop: 0,
				old: {
					scrollTop: 0
				}
			}
		},
		onLoad() {
			searchHot().then((res) => {
				if (res[1].data.code == '200') {
					this.searchHot = res[1].data.data;
				}
			});
			uni.getStorage({
				key: 'searchHistory',
				success: (res) => {
					this.historyList = res.data;
				}
			});
		},
		methods: {

			handleToSearch() {
				this.historyList.unshift(this.searchWord);
				this.historyList = [...new Set(this.historyList)];
				if (this.historyList.length > 10) {
					this.historyList.length = 10;
				}
				uni.setStorage({
					key: 'searchHistory',
					data: this.historyList
				});
				this.getSearchList(this.searchWord);
			},
			handleToClear() {
				uni.removeStorage({
					key: 'searchHistory',
					success: () => {
						this.historyList = [];
					}
				});
			},
			getSearchList(word) {
				searchWord(word).then((res) => {
					if (res[1].data.code == '200') {
						this.searchList = res[1].data.result.songs;
						this.searchType = 2;
					}
				});
			},
			handleToClose() {
				this.searchWord = '';
				this.searchType = 1;
			},
			handleToSuggest(ev) {
				let value = ev.detail.value;
				if (!value) {
					this.searchType = 1;
					return;
				}
				searchSuggest(value).then((res) => {
					if (res[1].data.code == '200') {
						this.suggestList = res[1].data.result.allMatch;
						this.searchType = 3;
					}
				});
			},
			handleToWord(word) {
				this.searchWord = word;
				this.handleToSearch();
			},
			handleToDetail(songId) {
				uni.navigateTo({
					url: '/pages/detail/detail?songId=' + songId
				});
			}
		}
	}
</script>


<style scoped>
	.music-list-view {
		/* background-color: #e5e5e5; */
		/* margin: 7px 7px 5px 2px; */
		margin:auto;
		padding: 1em;
	}

	.tb-js-yf-style {
		font: 12px/1 Tahoma, Helvetica, Arial, ”\5b8b\4f53”, sans-serif;
	}

	.box {
		display: flex;
	}

	.search-search {
		display: flex;
		background: #f7f7f7;
		height: 90rpx;
		margin: 28rpx 30rpx 30rpx 30rpx;
		border-radius: 50rpx;
		width: 800px;
		align-items: center;
	}

	.search-search text {
		margin: 0 27rpx;
	}

	.search-search input {
		font-size: 26rpx;
		flex: 1;
	}

	.search-history {
		margin: 0 30rpx;
		font-size: 26rpx;
	}

	.search-history-head {
		display: flex;
		justify-content: space-between;
	}

	.search-history-list {
		display: flex;
		margin-top: 25rpx;
		flex-wrap: wrap;

	}

	.search-history-list view {
		padding: 20rpx 40rpx;
		background: #f7f7f7;
		border-radius: 50rpx;
		margin-right: 30rpx;
		margin-bottom: 20rpx;
	}

	.search-hot {
		margin: 30rpx 30rpx;
		font-size: 26rpx;
		color: #bebebe;
	}

	.search-hot-title {}

	.search-hot-item {
		display: flex;
		align-items: center;
		margin-top: 40rpx;
	}

	.search-hot-top {
		width: 60rpx;
		color: #fb2221;
		font-size: 34rpx;
	}

	.search-hot-word {
		flex: 1;
		/* font-size: 15rpx; */
	}

	.search-hot-word view:nth-child(1) {
		font-size: 36rpx;
		color: black;
	}

	.search-hot-word image {
		width: 48rpx;
		height: 22rpx;
	}

	.search-hot-count {}

	.search-result {
		/* border-top:1px solid #000; */
		/* border-top: 1rpx #e5e5e5 solid; */
		/* background-color: #e5e5e5; */
		/* border-radius: 18px; */
		padding: 30rpx;
		border-width: 1em;
		margin: 10px 20px 20px;
		/* width: 150px; */
		
	}

	.search-result-item {
		display: flex;
		align-items: center;
		border-bottom: 2rpx #e5e5e5 solid;
		padding-bottom: 30rpx;
		margin-bottom: 30rpx;
	}

	.search-result-item text {
		font-size: 50rpx;
	}

	.search-result-word {
		flex: 1;
	}

	.search-result-word view:nth-child(1) {
		font-size: 28rpx;
		color: #3e6694;
	}

	.search-result-word view:nth-child(2) {
		font-size: 26rpx;
	}

	.search-suggest {
		border-top: 2rpx #e5e5e5 solid;
		padding: 30rpx;
		font-size: 26rpx;
	}

	.search-suggest-title {
		color: #537caa;
		margin-bottom: 40rpx;
	}

	.search-suggest-item {
		color: #666666;
		margin-bottom: 70rpx;
	}

	.search-suggest-item text {
		color: #c2c2c2;
		font-size: 26rpx;
		margin-right: 26rpx;
	}
</style>
