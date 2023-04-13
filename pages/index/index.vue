<template>
	<view class="content">
		<view class="top">
			<view class="left" @tap="prev">上一月</view>
			<uni-dateformat :date="`${year}-${month}-${toDay}`" format="yyyy-MM-dd"></uni-dateformat>
			<view class="left" @tap="next">下一月</view>
		</view>
		<view class="tab">
			<view class="item">日</view>
			<view class="item">一</view>
			<view class="item">二</view>
			<view class="item">三</view>
			<view class="item">四</view>
			<view class="item">五</view>
			<view class="item">六</view>
		</view>
		<view class="rl">
			<view v-for="d in weeks"  
			class="day" 
			:class="{
				'isWeekDay':d.isWeekDay}"
				
			>{{d.num}}</view>
				<!-- 'isToDay':d.isToday, -->
				<!-- 日历背景 -->
				<view class="bg">
					{{month}}
				</view>
			
		</view>
		<view class="btns">
			<!-- <button class="btn" @click="refresh">回到今天</button> -->
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				year: null,
				month: null,
				days: null,
				toDay: null,
				firstDayWeek: null,
				weeks:[],
				dayKey: 1 ,//  2 ： 1号  1 ： 2号  0 ： 3号
				initKey: 2,
				jg: 2
			}
		},
		onLoad() {	
		},
		mounted() {
			let D = new Date()
			this.year = D.getFullYear() // 今年
			this.month = D.getMonth() + 1 // 当月
			this.toDay = D.getDate() // 当天
			this.days = new Date(this.year, this.month, 0).getDate() // 当月有多少天
			this.init(this.year, this.month, this.days, 1)
			uni.onTabBarMidButtonTap(()=>{
				this.refresh()
			})
		},
		methods: {
			// 初始化日历
			init(y,m,days, type) {
				this.weeks = [] 
				if(type == 1) { // 下一月
					for (let i = 1; i <= days; i++) {	
						this.weeks.push({
							isToday: this.toDay == i ? true : false,
							num : i,
							// 是否为工作日
							isWeekDay:  (i + this.dayKey) % 3 === 0  ? true : false
						})
					}					
				} else if(type == 2) { // 上一月
					let n = -1
					let arr = []
					let num = days
					for (let i = days; i >= 1; i--) {
						if(i == days) {
							if(this.initKey == 2) {
								arr.unshift(num -= 0)
								
							}else if(this.initKey == 1) {
								arr.unshift(num -= 1)
								
							} else if(this.initKey == 0) {
								arr.unshift(num -= 2)
							}
						}else {
							arr.unshift(num -= 3)
						}
						if(num <= 0){
							break
						}  
						
					}
					console.log(arr);
					for (let i = days; i >= 1; i--) {
							this.weeks.unshift({
								isToday: this.toDay == i ? true : false,
								num : i,
								isWeekDay:  arr.includes(i) ? true : false
							})
					}	
							console.log('日历',this.weeks);
				}
				// 第一天是星期几  0 星期日
				this.firstDayWeek = new Date(`${y}/${m}/1`).getDay()
				console.log(this.firstDayWeek);
				for (let i = 0; i < this.firstDayWeek; i++) {
					this.weeks.unshift({
						isToDay: false,
						num: '',
						isWeekDay: false
					})
				}	
			},
			
			// 下一月
			next() {
				this.month ++
				let lastMonthDays = this.weeks.length
				console.log('点击：', this.month);
				console.log('上月天数：', lastMonthDays);
				
				
				// 本年
				if(this.month <= 12){
					this.days = new Date(this.year, this.month, 0).getDate() // 当月有多少天
					console.log(this.weeks);
					console.log(this.weeks[lastMonthDays - 1].isWeekDay);
					console.log(this.weeks[lastMonthDays - 2].isWeekDay);
					if(this.weeks[lastMonthDays - 1].isWeekDay) { // 本月最后一天是工作日
					console.log('进入了这个判断1111');
						this.dayKey = 0
					}else if(this.weeks[lastMonthDays - 2].isWeekDay) { // 本月倒数第二天是工作日
					console.log('进入了这个判断2222');
						this.dayKey = 1
					} else {
						console.log('进入了这个判断33333');
						this.dayKey = 2
					}
					this.init(this.year, this.month, this.days, 1)
					
				} else { // 下一年
					this.year ++
					this.month -= 12
					this.days = new Date(this.year, this.month, 0).getDate() // 当月有多少天
					if(this.weeks[lastMonthDays - 1].isWeekDay) { // 本月最后一天是工作日
						this.dayKey = 0
					}else if(this.weeks[lastMonthDays - 2].isWeekDay) { // 本月倒数第二天是工作日
						this.dayKey = 1
					} else {
						this.dayKey = 2
					}
					this.init(this.year, this.month, this.days, 1) 
				}
			},
			// 上一月
			prev() {
				this.month --
				let lastMonthDays = this.weeks.length
				console.log('点击：', this.month);
				console.log('上月天数：', lastMonthDays);
				
				let index = this.weeks.findIndex(it => it.num>0)
				console.log('index:', index);
				console.log(this.weeks);
				// 本年
				if(this.month > 1){
					this.days = new Date(this.year, this.month, 0).getDate() // 当月有多少天
					
					console.log(this.weeks[index].isWeekDay);
					console.log(this.weeks[index + 1].isWeekDay);
					 
					if(this.weeks[index].isWeekDay) { // 本月第一天是工作日
						console.log('进入了这个判断1111');
						this.initKey = 0
					}else if(this.weeks[index + 1].isWeekDay) { // 本月倒数第二天是工作日
						console.log('进入了这个判断2222');
						this.initKey = 1
					} else {
						console.log('进入了这个判断33333');
						this.initKey = 2
					}
					this.init(this.year, this.month, this.days, 2)
					
				} else { // 下一年
					this.year --
					this.month = 12
					this.days = new Date(this.year, this.month, 0).getDate() // 当月有多少天
					if(this.weeks[index].isWeekDay) { // 本月最后一天是工作日
						this.initKey = 0
					}else if(this.weeks[index + 1].isWeekDay) { // 本月倒数第二天是工作日
						this.initKey = 1
					} else {
						this.initKey = 2
					}
					this.init(this.year, this.month, this.days, 2) 
				}
			},
			// 回到今天
			refresh() {
				uni.showLoading({
					title: ''
				})
				location.reload()
			}
		}
	}
</script>

<style lang="scss">
	.content {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}
	.top {
		width: 100%;
		height: 80rpx;
		line-height: 80rpx;
		font-size: 28rpx;
		display: flex;
		border-bottom: 1rpx solid #f9d39f;
		margin-bottom: 15rpx;
		justify-content: space-between;
		.left {
			width: 180rpx;
			background-color: #fdedd9;
			text-align: center;
			border-radius: 16rpx;
			color: #f3a73f;
		}
	}
	.tab {
		display: flex;
		border-bottom: 1px solid #ccc;
		margin-bottom: 15rpx;

		.item {
			width: 100rpx;
			height: 80rpx;
			text-align: center;
			line-height: 80rpx;
			font-weight: bold;
			border: 1px solid transparent;
		}
	}

	.rl {
		
		display: grid;
		grid-template-columns: repeat(7, 1fr);
		grid-template-rows: repeat(5, 1fr);
		grid-column-gap: 0px;
		grid-row-gap: 10px;
		padding: 15px;
		position: relative;
		.day {
			// border-bottom: 1px solid #ccc;
			font-size: 32rpx;
			width: 100rpx;
			height: 100rpx;
			display: flex;
			align-items: center;
			justify-content: center;
			border-radius: 50%;
		}
		.isToDay {
			border: 1px solid #f00;
		}
		.isWeekDay {
			background-color: #f9d39f;
			color: #fff;
			
		}
		.bg {
			position: absolute;
			width: 100%;
			height:100%;
			z-index: -1;
			display: flex;
			align-items: center;
			justify-content: center;
			font-size: 150px;
			color: #dedede;
			font-weight: 800;
		}
	}
	.btns {
		position: absolute;
		bottom: 0;
		border: none;
		.btn {
			color: #fff ;
			background-color: #f9d39f;
			border-radius: 10px;
			border:1px solid #f9d39f;
		}
		uni-button:after {
			border: none;
		}
	}
</style>
