<template>
	<view>
		<view class="cu-progress" :style="{'border-radius':bgBR,'width': cuProgressW,'height':cuProgressH,'background-color': backgroundColor}">
			<progress class="progress" duration="1" :style="{'margin-left':progressML,'width': progressW,'height':progressH}" :border-radius="borderRadius" :font-size="fontSize" :percent="pgsValue" :stroke-width="strokeWidth" :activeColor="activeColor" :backgroundColor="noActiveColor"></progress>
			<movable-area class="area" :style="{width:areaW,height:areaH}" @touchcancel="touchCancel" @touchstart="areaTouchStart" @touchmove="areaTouchMove" @touchend="touchEnd">
				<movable-view class="handle" :animation="false" :style="{'top': handleT,'width':handleS,'height':handleS,'border-radius': handleBR,'background-color':handleColor}"  @change="change" :damping="damping" :x="handleX" :y="handleY" direction="horizontal">
				 <text :class="handleIcon" :style="{'backgroundColor':iconColor,'font-size':iconS,'border-radius':iconBR}"></text>
				 </movable-view>
			</movable-area>
			<text v-if="showInfo == true || showInfo == 'true'" class="showInfo" :style="{'color':fontColor,'font-size': infoS,width:infoW}">{{showValue}}{{infoEndText}}</text>
		</view>
	</view>
</template>

<script>
	export default {
		name: 'cu-progress',
		data() {
			return {
				scale: 1,
				pgsValue: 0, // progress进度
				showValue: 0,//显示进度
				showVL: 0, // showValue 最大长度
				handleMoveStatus: false,
				handleX: 0, // 拖柄位置
				handleY: 0,
				progressInfo: {
					left: 0,
					width: 0,
				},
				area: {
					left: 0,
					width: 0,
					height: 0
				},
				handle: {
					height: 0
				}
			};
		},
		beforeMount: function() {
			const res = uni.getSystemInfoSync()
			this.scale = 750 / res.windowWidth;
			let tl = this.textLength(this.infoEndText)
			// 0 为 false
			this.showValue = Number.isNaN(parseInt(this.value)) ? this.minValue : parseInt(this.value)
			if (this.maxValue - this.minValue == 0){
				console.error("min不能等于max"+this.maxValue + this.minValue)
				return
			}
			this.pgsValue = Math.abs(this.showValue - this.minValue) / Math.abs(this.maxValue - this.minValue) * 100
			let minl = this.textLength(this.minValue.toString() + this.infoEndText)
			let maxl = this.textLength(this.maxValue.toString() + this.infoEndText)
			this.showVL = maxl > minl ? maxl : minl
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
					this.area.height = data.height
					const s = this.sizeDeal(this.handleSize)
					this.handleX = this.area.width * this.pgsValue / 100 - s[0]/2
				}).exec()
				query.select(".handle").boundingClientRect(data => {
					this.handle.height = data.height
				}).exec()
			})
		},
		props: {
			bgBorderRadius: {
				default: 0,
				type: [Number, String]
			},
			iconBorderRadius: {
				default: '8px',
				type: [Number, String]
			},
			iconColor: {
				default: 'inherit',
				type: String
			},
			iconSize: {
				default: '8px',
				type: [Number, String]
			},
			handleIcon: {
				default: '',
				type: String
			},
			infoEndText: {
				default: '',
				type: String
			},
			backgroundColor: {
				default: 'inherit',
				type: String
			},
			max: {
				default: 100,
				type: [String, Number]
			},
			min: {
				default: 0,
				type: [String, Number]
			},
			value: {
				default: null, // 设置进度条进度
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
				default: '3', // 进度条宽度
				type: [String, Number]
			},
			damping: {
				default: 100, // 阻尼系数，越大移动越快
				type: [String, Number]
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
				default: false, // 是否在进度条右侧显示百分比
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
			bgBR (){
				return this.sizeDeal(this.bgBorderRadius)[2]
			},
			iconBR (){
				return this.sizeDeal(this.iconBorderRadius)[2]
			},
			iconS (){
				return this.sizeDeal(this.iconSize)[2]
			},
			infoW (){
				const s = this.sizeDeal(this.fontSize)
				const size = s[0]*this.showVL + s[1]
				return size
			},
			infoS (){
				return this.sizeDeal(this.fontSize)[2]
			},
			areaW (){
				const s = this.sizeDeal(this.fontSize)
				const h = this.maxHeight()
				let w
				if (this.showInfo == true || this.showInfo == 'true'){
					w = 'calc(' + this.width + ' - ' + s[0]*this.showVL + s[1] + ')'
				}
				else{
					w = this.width
				}
				return w
			},
			areaH (){
				return this.maxHeight()[2]
			},
			progressW (){
				const s = this.sizeDeal(this.fontSize)
				const w = this.sizeDeal(this.width)
				const s2 = this.sizeDeal(this.handleSize)
				let w2
				if (this.showInfo == true || this.showInfo == 'true'){
					w2 = 'calc(' + w[2] + ' - ' + s[0]*this.showVL + s[1] + ' - ' + s2[2] + ')'
				}
				else{
					w2 = 'calc(' + w[2] + ' - ' + s2[2] + ')'
				}
				return w2
			},
			progressH (){
				const h = this.sizeDeal(this.strokeWidth)
				return h[2]
			},
			progressML (){
				let style
				const s = this.sizeDeal(this.fontSize)
				const s2 = this.sizeDeal(this.handleSize)
				return s2[0]/2 + s[1]
			},
			handleS (){
				const s = this.sizeDeal(this.handleSize)
				return s[2]
			},
			handleT (){
				if (this.area.height){
					const s = this.sizeDeal(this.handleSize)
					const ah = Number(this.area.height) / 2
					const t = ah - s[0] / 2 + 'px'
					return t
				}
			},
			handleBR (){
				const r = this.sizeDeal(this.handleBorderRadius)
				return r[2]
			},
			cuProgressW (){
				return this.width
			},
			cuProgressH (){
				const h = this.maxHeight()
				return h[2]
			},
			maxValue (){
				let max = Number.isNaN(parseInt(this.max)) ? 100 : parseInt(this.max)
				return max
			},
			minValue (){
				let min = Number.isNaN(parseInt(this.min)) ? 0 : parseInt(this.min)
				return min
			},
		},
		watch: {
			value (v){
				// 当处于拖动状态时，禁止进度条外部触发变化
				if (!this.handleMoveStatus){
					this.showValue = Number.isNaN(parseInt(v)) ? this.minValue : parseInt(v)
					this.pgsValue = (this.showValue - this.minValue) / Math.abs(this.maxValue - this.minValue) * 100
					const s = this.sizeDeal(this.handleSize)
					this.handleX = this.area.width * this.showValue / (this.maxValue - this.minValue) - s[0]/2
				}
			}
		},
		methods: {
			textLength(t){
				t = t.toString()
				let subt = t.match(/[^\x00-\xff]/g)
				let subl = 0
				if (subt){subl = subt.length}
				let l = (t.length - subl) / 3 * 2 + subl
				l = Number(l.toFixed(2))
				return l
			},
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
				let s = Number.isNaN(parseInt(size)) ? 0 : parseInt(size)
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
			change(e){
				this.$emit('change',{value:this.showValue})
			},
			handleMove (x){
				x = x >= 0 ? x : 0
				let s = this.sizeDeal(this.handleSize)
				let cp = x / (this.area.width - s[0] - 1) // 多减 1 避免达不到maxValue
				cp = cp <= 1 ? cp : 1
				let sv = parseInt((cp * (this.maxValue - this.minValue)).toFixed(0)) + this.minValue
				this.pgsValue = cp * 100
				this.showValue = sv
				this.handleX = x
				this.$emit('dragging',{value:this.showValue})
			},
			touchEnd (){
				this.handleMoveStatus = false
				this.$emit('touchend',{value:this.showValue})
				this.$emit('dragged',{value:this.showValue})
			},
			areaTouchStart (e){
				this.handleMoveStatus = true
				let s = this.sizeDeal(this.handleSize)
				let tapX = e.changedTouches[0].clientX - this.area.left - s[0] / 2 // 拖柄中心与鼠标箭头一致
				this.handleMove(tapX)
				this.$emit('touchstart',{value:this.showValue})
			},
			areaTouchMove (e){
				let s = this.sizeDeal(this.handleSize)
				let tapX = e.changedTouches[0].clientX - this.area.left - s[0] / 2
				tapX = tapX >= 0 ? tapX : 0
				this.handleMove(tapX)
				this.$emit('touchmove',{value:this.showValue})
			},
			touchCancel (){
				this.touchEnd()
				this.$emit('touchcancel',{value:this.showValue})
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
		background-color: inherit;
	}
	.area {
		position: absolute;
		display: flex;
		flex-direction: row;
		align-items: center;
		left: 0;
		z-index: 1;
	}
	.handle {
		position: absolute;
		display: flex;
		flex-direction: row;
		align-items: center;
		text-align: center;
		justify-content: space-around;
		overflow: hidden;
		z-index: 2;
	}
	.handle text{
		align-items: center;
		text-align: center;
		/* overflow: hidden; */
		white-space: nowrap;
	}
	.showInfo {
		justify-content: space-around;
		align-items: center;
		overflow: hidden;
		white-space: nowrap;
	}
</style>
