<template>
	<view>
		<view class="cu-progress" :style="cuProgressStyle">
			<progress class="progress" :style="progressStyle" :border-radius="borderRadius" :font-size="fontSize" :percent="cuPercent" :stroke-width="strokeWidth" :activeColor="activeColor" :backgroundColor="noActiveColor"></progress>
			<movable-area class="area" :style="areaStyle" @touchstart="areaTouchStart" @touchmove="areaTouchMove" @touchend="areaTouchEnd">
				<movable-view class="handle" @touchstart="handleTouchStart" @touchend="handleTouchEnd" @change="handleMove" :damping="damping" :animation="animation" :x="handleX" :y="handleY" direction="horizontal" :style="handleStyle"></movable-view>
			</movable-area>
			<text v-if="showInfo == true || showInfo == 'true'" class="showInfo" :style="infoStyle">{{cuPercent}}%</text>
		</view>
	</view>
</template>

<script>
	export default {
		name: 'cu-progress',
		data() {
			return {
				scale: 1,
				cuPercent: this.percent,
				handleMoveStatus: false,
				handleX: 0,
				handleY: 0,
				progressInfo: {
					left: 0,
					width: 0,
				},
				area: {
					left: 0,
					width: 0,
				},
			};
		},
		beforeMount: function() {
			const res = uni.getSystemInfoSync()
			this.scale = 750 / res.windowWidth;
		},
		mounted: function (){
			this.$nextTick(function(){
				const query = uni.createSelectorQuery().in(this)
				query.select(".progress").boundingClientRect(data => {
					this.progressInfo.width = data.width
					this.progressInfo.left = data.left
				}).exec()
				query.select(".area").boundingClientRect(data => {
					this.area.width = data.width
					this.area.left = data.left
					const s = this.sizeDeal(this.handleSize)
					this.handleX = this.area.width * this.percent / 100 - s[0]/2
				}).exec()
			})
		},
		props: {
			percent: {
				default: 0, // 设置进度条进度
				type: [String, Number]
			},
			activeColor: {
				default: '#444444', // 已经过区域颜色
				type: String
			},
			noActiveColor: {
				default: '#888888', // 为经过区域颜色
				type: String
			},
			strokeWidth: {
				default: 3, // 进度条宽度
				type: [String, Number]
			},
			animation: {
				default: false, // 是否使用动画
				type: Boolean
			},
			damping: {
				default: 20, // 阻尼系数，越大移动越快
				type: Number
			},
			handleColor: {
				default: '#000000', // 拖柄颜色
				type: String
			},
			handleSize: {
				default: '8px', // 拖柄尺寸
				type: [String, Number]
			},
			handleBorderRadius: {
				default: '8px', // 拖柄圆角半径
				type: [String, Number]
			},
			showInfo: {
				default: true, // 是否在进度条右侧显示百分比
				type: [Boolean, String]
			},
			fontSize: {
				default: '16px', // 百分比显示字体尺寸
				type: [String, Number]
			},
			fontColor: {
				default: '#000000', // 百分比显示字体颜色
				type: String
			},
			borderRadius: {
				default: 0, // 进度条圆角半径
				type: [String, Number]
			},
			width: {
				default: '200px', // 进度条总宽，如果显示百分比，则包括百分比在内的宽度
				type: [String, Number]
			},
		},
		computed: {
			handleStyle (){
				const s = this.sizeDeal(this.handleSize)
				const r = this.sizeDeal(this.handleBorderRadius)
				return {
					width:s[2],
					height:s[2],
					'border-radius': r[2],
					'background-color':this.handleColor,
					}
			},
			progressStyle (){
				let style
				const s = this.sizeDeal(this.fontSize)
				const h = this.sizeDeal(this.strokeWidth)
				const w = this.sizeDeal(this.width)
				const s2 = this.sizeDeal(this.handleSize)
				if (this.showInfo == true || this.showInfo == 'true'){
					style = {'margin-left':s2[0]/2 + s[1],width:'calc(' + w[2] + ' - ' + s[0]*3 + s[1] + ' - ' + s2[2] + ')',height:this.strokeWidth}
				}
				else{
					style = {'margin-left':s2[0]/2 + s[1],width:'calc(' + w[2] + ' - ' + s2[2] + ')',height:h[2]}
				}
				return style
			},
			areaStyle (){
				let style
				const s = this.sizeDeal(this.fontSize)
				const h = this.maxHeight()
				if (this.showInfo == true || this.showInfo == 'true'){
					style = {width:'calc(' + this.width + ' - ' + s[0]*3 + s[1] + ')',height:h[2]}
				}
				else{
					style = {width:this.width,height:h[2]}
				}
				return style
			},
			infoStyle (){
				const s = this.sizeDeal(this.fontSize)
				const size = s[0]*3 + s[1]
				return {color:this.fontColor,'font-size': s[2],width:size,height:s[2]}
			},
			cuProgressStyle (){
				const h = this.maxHeight()
				if (this.showInfo == true || this.showInfo == 'true'){
					return {width: this.width,height:h[2]}
				}
				else{
					return {width: this.width,height:h[2]}
				}
			}
		},
		watch: {
			percent (v){
				// 当处于拖动状态时，禁止进度条外部触发变化
				if (!handleMoveStatus){
					this.cuPercent = v
					const s = this.sizeDeal(this.handleSize)
					this.handleX = this.area.width * this.percent / 100 - s[0]/2
				}
			}
		},
		methods: {
			maxHeight (){
				let h = []
				h.push(this.sizeDeal(this.fontSize)[0])
				h.push(this.sizeDeal(this.strokeWidth)[0])
				h.push(this.sizeDeal(this.handleSize)[0])
				h.sort(function (a,b){return b - a}) // 降序
				return [h[0],'px',h[0] + 'px']
			},
			sizeDeal (size){
				// 分离字体大小和单位,rpx 转 px
				let s = parseInt(size)
				let u = size.toString().replace(/[0-9]/g, '')
				if (u == 'rpx'){
					s /= this.scale 
					u = 'px'
				}
				else if (u == ''){
					u = 'px'
				}
				return [s,u,s.toString()+u]
			},
			handleMove (e){
				if (this.handleMoveStatus){
					let tapX = e.detail.x
					tapX = tapX >= 0 ? tapX : 0
					let s = this.sizeDeal(this.handleSize)
					let cuPercent = tapX / (this.area.width - s[0])
					cuPercent = cuPercent <= 1 ? cuPercent : 1
					this.cuPercent = (cuPercent * 100).toFixed(0)
					this.handleX = tapX
					this.$emit('change',{percent:this.cuPercent})
				}
			},
			handleTouchStart (e){
				this.handleMoveStatus = true
			},
			handleTouchEnd (){
				this.handleMoveStatus = false
			},
			areaTouchStart (e){
				this.handleMoveStatus = true
				let tapX = e.changedTouches[0].clientX - this.area.left
				tapX = tapX >= 0 ? tapX : 0
				this.handleX = tapX
			},
			areaTouchMove (e){
				if (this.handleMoveStatus){
					let tapX = e.changedTouches[0].clientX - this.area.left
					tapX = tapX >= 0 ? tapX : 0
					this.handleX = tapX
				}
			},
			areaTouchEnd (){
				this.handleMoveStatus = false
			},
		}
	}
</script>

<style scoped>
	.cu-progress {
		position: relative;
		display: flex;
		flex-direction: row;
		align-items: center;
		text-align: center;
		justify-content: space-between;
		margin: 0;
		padding: 0;
	}
	.area {
		position: absolute;
		display: flex;
		flex-direction: row;
		left: 0;
		align-items: center;
	}
	.handle {
		position: relative;
		z-index: 2;
	}
	.showInfo {
		justify-content: space-evenly;
		text-align: right;
		align-items: center;
	}
</style>
