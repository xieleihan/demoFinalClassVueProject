<template>
	<view class="main">
		<view class="uni-margin-wrap">
			<swiper class="swiper" circular :indicator-dots="indicatorDots" :autoplay="autoplay" :interval="interval"
				:duration="duration">
				<swiper-item v-for="(image, index) in images" :key="index">
					<view class="swiper-item">
						<image :src="image" class="swiper-image"></image>
					</view>
				</swiper-item>
			</swiper>
		</view>
		<view class="bigTitle">云上书库</view>
		<scroll-view :scroll-top="scrollTop" scroll-y="true" class="scroll-Y" @scrolltoupper="upper"
			@scrolltolower="lower" @scroll="scroll">
			<view v-for="book in books" :key="book.id" class="item">
				<image :src="book.bookpic" class="images" mode="aspectFit" />
				<view class="bookInfo">
					<text class="title">{{ book.booktitle }}</text>
					<text class="info">{{ book.bookinfo }}</text>
				</view>
			</view>
		</scroll-view>
	</view>
</template>


<script>
	export default {
		data() {
			return {
				indicatorDots: true,
				autoplay: true,
				interval: 2000,
				duration: 500,
				images: [], // 初始化为空数组
				books: [],
				scrollTop: 0
			}
		},
		onLoad() {
			this.fetchBooks();
		},
		mounted() {
			this.generateRandomImages();
		},
		methods: {
			generateRandomImages() {
				const baseUrl = 'https://picsum.photos/1920/1080'; // 替换为你的基础URL
				for (let i = 0; i < 6; i++) {
					const randomNum = Math.floor(Math.random() * 100); // 生成随机数
					this.images.push(`${baseUrl}?${randomNum}`);
				}
			},
			fetchBooks() {
				uni.request({
					url: 'http://localhost:2282/bookinfo/books',
					method: 'GET',
					success: (response) => {
						if (response.statusCode === 200) {
							this.books = response.data.map(book => {
								const imageUrl = `data:image/png;base64,${book.bookpic}`;
								return {
									...book,
									bookpic: imageUrl
								};
							});
							console.log('Books fetched successfully:', this.books);
						} else {
							console.error('Error fetching books:', response);
						}
					},
					fail: (error) => {
						console.error('Error fetching books:', error);
					}
				});
			},
			upper() {
				// 滑动到顶部时的处理函数
				console.log('Scrolled to the top');
			},
			lower() {
				// 滑动到底部时的处理函数
				console.log('Scrolled to the bottom');
			},
			scroll() {
				// 滑动过程中触发的处理函数
				console.log('Scrolling');
			}
		}
	}
</script>

<style scoped>
	.scroll-Y {
		height: calc(100dvh - 190rpx - 400rpx);
		display: flex;
		align-items: center;
		flex-direction: column;
	}

	.main {
		width: 100dvw;
		height: calc(100dvh - 190rpx);
		display: flex;
		flex-direction: column;
		align-items: center;
	}

	.main .bigTitle {
		font-weight: bold;
		font-size: 50rpx;
		margin-top: 40rpx;
	}

	.main .item {
		width: 90%;
		height: 30%;
		border: 1rpx black solid;
		border-radius: 20rpx;
		display: flex;
		flex-direction: row;
		justify-content: space-evenly;
		align-items: center;
		margin: 50rpx auto;
	}

	.main .item .images {
		width: 30%;
		height: 90%;
		background-color: skyblue;
		margin-left: 20rpx;
	}

	.main .item .bookInfo {
		width: 50%;
		height: 90%;
	}

	.main .item .bookInfo .title {
		font-weight: bold;
		font-size: 40rpx;
		font-family: "PingFang SC";
		margin-bottom: 40rpx;
	}

	.main .item .bookInfo .info {
		height: 50%;
		overflow: hidden;
		-webkit-line-clamp: 3;
		text-overflow: ellipsis;
		display: -webkit-box;
		-webkit-box-orient: vertical;
	}

	.main .uni-margin-wrap {
		width: 690rpx;
		width: 100%;
	}

	.main .uni-margin-wrap .swiper {
		height: 300rpx;
	}

	.main .uni-margin-wrap .swiper .swiper-item {
		display: block;
		height: 300rpx;
		line-height: 300rpx;
		text-align: center;
	}

	.main .uni-margin-wrap .swiper .swiper-item .swiper-image {
		width: 100%;
		height: 100%;
	}
</style>