<template>
	<view>
		<view class="cu-progress-main" :style="{'border-radius':bgBR,'width': progressMainW,'height':progressMainH,'background-color': backgroundColor}">
			<view class="cu-progress-bar" :style="{'position':pgPn, 'bottom':progressBt,'background':pgbg,'margin-left':progressML, 'margin-bottom':progressMB,'width': progressW,'height':progressH,'border-radius':pgBR,'background-color':noActiveColor}"></view>
			<movable-area class="cu-area" :style="{'flex-direction':flexDirection,'width':areaW,height:areaH}" @touchcancel="touchCancel" @touchstart="areaTouchStart"
			 @touchmove="areaTouchMove" @touchend="touchEnd">
				<movable-view class="cu-handle" disabled="disabled" :animation="false" :style="{ 'top':handleT, 'width':handleS,'height':handleS,'border-radius': handleBR,'background-color':handleColor}"
				 @change="change" :damping="damping" :x="handleX" :y="handleY" :direction="direction == 'vertical' ? 'vertical' : 'horizontal'">
					<text :class="handleIcon" :style="{'backgroundColor':iconColor,'font-size':iconS,'border-radius':iconBR}"></text>
				</movable-view>
			</movable-area>
			<text v-if="showInfoStatus" class="cu-showInfo" :style="{'color':infoColor,'font-size':infoS,width:infoW}">{{showValue}}{{infoEndText}}</text>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				scale: 1,
				percent: 0, // progress进度
				// pgbw: '10px', // progress-bar width
				showValue: 0, //显示进度
				showVL: 0, // showValue 最大长度
				handleMoveStatus: false,
				handleX: 0, // 拖柄位置
				handleY: 0,
				progressInfo: {
					left: 0,
					bottom: 0,
					width: 0,
					height: 0
				},
				area: {
					left: 0,
					bottom: 0,
					width: 0,
					height: 0,
					top: 0
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
			if (this.maxValue - this.minValue == 0) {
				console.error("min不能等于max:" + this.minValue + "->" + this.maxValue)
				return
			}
			this.percent = Math.abs(this.showValue - this.minValue) / Math.abs(this.maxValue - this.minValue) * 100
			let minl = this.textLength(this.minValue.toString() + this.infoEndText)
			let maxl = this.textLength(this.maxValue.toString() + this.infoEndText)
			this.showVL = maxl > minl ? maxl : minl
		},
		mounted: function() {
			this.$nextTick(function() {
				const query = uni.createSelectorQuery().in(this)
				query.select(".cu-progress-bar").boundingClientRect(data => {
					this.progressInfo.width = data.width
					this.progressInfo.left = data.left
					this.progressInfo.bottom = data.bottom
					this.progressInfo.height = data.height
					if (this.direction == 'vertical'){
						this.handleY = this.progressInfo.height * (100 - this.percent) / 100
					}
					else{
						this.handleX = this.progressInfo.width * this.percent / 100
					}
				}).exec()
				query.select(".cu-area").boundingClientRect(data => {
					this.area.width = data.width
					this.area.left = data.left
					this.area.height = data.height
					this.area.bottom = data.bottom
					this.area.top = data.top
				}).exec()
				query.select(".cu-handle").boundingClientRect(data => {
					this.handle.height = data.height
				}).exec()
			})
		},
		props: {
			direction: {
				default: 'horizontal', //vertical  方向
				type: String
			},
			disabled: {
				default: false,
				type: [String, Boolean]
			},
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
				default: '3', // 行程宽度
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
			infoSize: {
				default: '16px', // 百分比显示字体尺寸
				type: [String, Number]
			},
			infoColor: {
				default: '#000000', // 百分比显示字体颜色
				type: String
			},
			infoEndText: {
				default: '',
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
			showVLC (){
				let minl = this.textLength(this.minValue.toString() + this.infoEndText)
				let maxl = this.textLength(this.maxValue.toString() + this.infoEndText)
				
				let L = maxl > minl ? maxl : minl
				return L
			},
			pgPn (){
				// progress position
				if (this.direction == 'vertical'){
					return 'absolute'
				}
				else{
					return 'relative'
				}
			},
			flexDirection (){
				if (this.direction == 'vertical'){
					return 'column'
				}
				else{
					return 'row'
				}
			},
			progressBt (){
				if (this.direction == 'vertical'){
					return '0px'
				}
				else{
					return 'unset'
				}
			},
			showInfoStatus (){
				if ((this.showInfo == true || this.showInfo == 'true') && this.direction != "vertical"){
					return true
				}
				else{
					return false
				}
			},
			pgbg(){
				let bg1
				if (this.direction == 'vertical'){
					bg1 = "linear-gradient(to top," + this.activeColor + ' ' + this.percent + "%," + this.noActiveColor + ' ' + this.percent + "%)"
				}
				else{
					bg1 = "linear-gradient(to right," + this.activeColor + ' ' + this.percent + "%," + this.noActiveColor + ' ' + this.percent + "%)"
					// qq
					let bg2 = "-webkit-linear-gradient(left," + this.activeColor + ' ' + this.percent + "%," + this.noActiveColor + ' ' + this.percent + "%)"
					let bg3 = "-o-linear-gradient(right," + this.activeColor + ' ' + this.percent + "%," + this.noActiveColor + ' ' + this.percent + "%)"
					let bg4 = "-moz-linear-gradient(right," + this.activeColor + ' ' + this.percent + "%," + this.noActiveColor + ' ' + this.percent + "%)"
				}
				return bg1
			},
			pgBR(){
				return this.sizeDeal(this.borderRadius)[2]
			},
			bgBR() {
				return this.sizeDeal(this.bgBorderRadius)[2]
			},
			iconBR() {
				return this.sizeDeal(this.iconBorderRadius)[2]
			},
			iconS() {
				return this.sizeDeal(this.iconSize)[2]
			},
			infoW() {
				const s = this.sizeDeal(this.infoSize)
				const size = s[0] * this.showVL + s[1]
				return size
			},
			infoS() {
				return this.sizeDeal(this.infoSize)[2]
			},
			areaW: {
				get (){
					if (this.direction == 'vertical'){
						return this.maxHeight(true)[2]
					}
					else{
						const s = this.sizeDeal(this.infoSize)
						const h = this.maxHeight()
						let w
						if (this.showInfo == true || this.showInfo == 'true') {
							w = 'calc(' + this.width + ' - ' + s[0] * this.showVL + s[1] + ')'
						} else {
							w = this.width
						}
						return w
					}
				},
				set (w){
					return w
				}
				
			},
			areaH: {
				get(){
					if (this.direction == 'vertical'){
						return this.width
					}
					else{
						return this.maxHeight()[2]
					}
				},
				set (v){
					return v
				}
				
			},
			progressW() {
				// width
				if (this.direction == 'vertical'){
					return this.sizeDeal(this.strokeWidth)[2]
				}
				else{
					const s = this.sizeDeal(this.infoSize)
					const w = this.sizeDeal(this.width)
					const s2 = this.sizeDeal(this.handleSize)
					let w2
					if (this.getShowInfoStatus()) {
						w2 = 'calc(' + w[2] + ' - ' + s[0] * this.showVL + s[1] + ' - ' + s2[2] + ')'
					} else {
						w2 = 'calc(' + w[2] + ' - ' + s2[2] + ')'
					}
					return w2
				}
			},
			progressH() {
				// height
				if (this.direction == 'vertical'){
					const w = this.sizeDeal(this.width)
					const s2 = this.sizeDeal(this.handleSize)
					let	w2 = 'calc(' + w[2] + ' - ' + s2[2] + ')'
					return w2
				}
				else{
					return this.sizeDeal(this.strokeWidth)[2]
				}
			},
			progressL (){
				if (this.direction == 'vertical') {
					const s = this.sizeDeal(this.progressInfo.width)
					const ah = Number(this.area.width) / 2
					const t = ah - s[0] / 2 + 'px'
					return t
				}
				else{
					return 'unset'
				}
			},
			progressML() {
				// margin-left
				if (this.direction == 'vertical'){
					const s = this.sizeDeal(this.progressInfo.width)
					const ah = Number(this.area.width) / 2
					const t = ah - s[0] / 2 + 'px'
					return t
				}
				else{
					const s2 = this.sizeDeal(this.handleSize)
					return s2[0] / 2 + s2[1]
				}
			},
			progressMB() {
				// margin-bottom
				if (this.direction == 'vertical'){
					const s2 = this.sizeDeal(this.handleSize)
					return s2[0] / 2 + s2[1]
				}
				else{
					return 0
				}
			},
			handleS() {
				const s = this.sizeDeal(this.handleSize)
				return s[2]
			},
			handleL (){
				if (this.direction == 'vertical') {
					const s = this.sizeDeal(this.handleSize)
					const ah = Number(this.area.width) / 2
					const t = ah - s[0] / 2 + 'px'
					return t
				}
				else{
					return 'unset'
				}
			},
			handleT() {
				if (this.area.height && this.direction != 'vertical') {
					const s = this.sizeDeal(this.handleSize)
					const ah = Number(this.area.height) / 2
					const t = ah - s[0] / 2 + 'px'
					return t
				}
				else{
					return 'unset'
				}
			},
			handleBR() {
				const r = this.sizeDeal(this.handleBorderRadius)
				return r[2]
			},
			progressMainW() {
				let w
				if (this.direction == 'vertical'){
					w = this.maxHeight(true)[2]
				}
				else{
					w = this.width
				}
				return w
			},
			progressMainH() {
				let h
				if (this.direction == 'vertical'){
					h = this.width
				}
				else{
					h = this.maxHeight()[2]
				}
				return h
			},
			maxValue() {
				let max = Number.isNaN(parseInt(this.max)) ? 100 : parseInt(this.max)
				return max
			},
			minValue() {
				let min = Number.isNaN(parseInt(this.min)) ? 0 : parseInt(this.min)
				return min
			},
		},
		watch: {
			showVLC (v){
				this.showVL = v
				if (this.direction != 'vertical'){
					const s = this.sizeDeal(this.infoSize)
					const h = this.maxHeight()
					let w
					if (this.showInfo == true || this.showInfo == 'true') {
						w = 'calc(' + this.width + ' - ' + s[0] * this.showVL + s[1] + ')'
					} else {
						w = this.width
					}
					this.areaW = w
					this.areaH = h[2]
				}
				this.clientInit()
			},
			value(v) {
				// 当处于拖动状态时，禁止进度条外部触发变化
				if (!this.handleMoveStatus) {
					this.showValue = Number.isNaN(parseInt(v)) ? this.minValue : parseInt(v)
					this.percent = (this.showValue - this.minValue) / Math.abs(this.maxValue - this.minValue) * 100
					if (this.direction == 'vertical'){
						this.handleY = this.progressInfo.height * this.percent / 100
					}
					else{
						this.handleX = this.progressInfo.width * this.percent / 100
					}
				}
			},
		},
		methods: {
			clientInit() {
				this.$nextTick(function() {
					const query = uni.createSelectorQuery().in(this)
					query.select(".cu-progress-bar").boundingClientRect(data => {
						this.progressInfo.width = data.width
						this.progressInfo.left = data.left
						this.progressInfo.bottom = data.bottom
						this.progressInfo.height = data.height
						if (this.direction == 'vertical'){
							this.handleY = this.progressInfo.height * this.percent / 100
						}
						else{
							this.handleX = this.progressInfo.width * this.percent / 100
						}
					}).exec()
					query.select(".cu-area").boundingClientRect(data => {
						this.area.width = data.width
						this.area.left = data.left
						this.area.height = data.height
						this.area.bottom = data.bottom
						this.area.top = data.top
					}).exec()
					this.showValue = Number.isNaN(parseInt(this.value)) ? this.minValue : parseInt(this.value)
					if (this.maxValue - this.minValue == 0) {
						console.error("min不能等于max:" + this.minValue + "->" + this.maxValue)
					}
					else{
						this.showValue = parseInt(this.percent * (this.maxValue - this.minValue) / 100)
					}
				})
			},
			getShowInfoStatus(){
				if ((this.showInfo == true || this.showInfo == 'true') && this.direction != "vertical"){
					return true
				}
				else{
					return false
				}
			},
			textLength(t) {
				t = t.toString()
				let subt = t.match(/[^\x00-\xff]/g)
				let subl = 0
				if (subt) {
					subl = subt.length
				}
				let l = (t.length - subl) / 3 * 2 + subl
				l = Number(l.toFixed(2))
				return l
			},
			maxHeight(vertical) {
				let h = []
				if (!vertical){ // direction 为 vertical 时不显示info
					let subt = this.infoEndText.match(/[^\x00-\xff]/g)
					if (subt){
						h.push(this.sizeDeal(this.infoSize)[0] * 1.1)
					}
					else{
						h.push(this.sizeDeal(this.infoSize)[0])
					}
				}
				h.push(this.sizeDeal(this.strokeWidth)[0])
				h.push(this.sizeDeal(this.handleSize)[0])
				h.sort(function(a, b) {
					return b - a
				}) // 降序
				return [h[0], 'px', h[0] + 'px']
			},
			sizeDeal(size) {
				// 分离字体大小和单位,rpx 转 px
				let s = Number.isNaN(parseFloat(size)) ? 0 : parseFloat(size)
				let u = size.toString().replace(/[0-9]/g, '')
				if (u == 'rpx') {
					s /= this.scale
					u = 'px'
				} else if (u == '') {
					u = 'px'
				}else if (u == 'vw') {
					u = 'px'
					s = s / 100 * 750 / this.scale
				}
				return [s, u, s + u]
			},
			change (){
				this.$emit('change', {
					type: 'change',
					value: this.showValue,
					})
			},
			handleMove(x_) {
				let x = x_
				x = x >= 0 ? x : 0
				let s = this.sizeDeal(this.handleSize)
				let cp 
				let sv
				if (this.direction == 'vertical'){
					// #ifdef H5
					x = x - s[0] / 2 - this.area.bottom // ?
					cp = x / (this.area.height - s[0] - 1)
					// #endif
					// #ifndef H5
					x = x - this.area.top - s[0] / 2
					cp = x / (this.area.height - s[0] - 1)
					// #endif
					cp = cp <= 1 ? cp : 1
					cp = cp > 0 ? cp : 0
					sv = parseInt(((1 - cp) * (this.maxValue - this.minValue)).toFixed(0)) + this.minValue
					this.percent = (1 - cp) * 100
					this.showValue = sv
					this.handleY = x
				}
				else{
					cp = x / (this.area.width - s[0] - 1) // 多减 1 避免达不到maxValue
					cp = cp <= 1 ? cp : 1
					cp = cp > 0 ? cp : 0
					sv = parseInt((cp * (this.maxValue - this.minValue)).toFixed(0)) + this.minValue
					this.percent = cp * 100
					this.showValue = sv
					this.handleX = x
				}
				this.$emit('dragging', {
					type: 'dragging',
					value: this.showValue
				})
			},
			touchEnd() {
				if (!this.disabled){
					this.handleMoveStatus = false
					this.$emit('dragged', {
						type: 'dragged',
						value: this.showValue
					})
				}
			},
			areaTouchStart(e) {
				if (!this.disabled){
					this.handleMoveStatus = true
					let s = this.sizeDeal(this.handleSize)
					let tapX
					if (this.direction == 'vertical'){
						// #ifdef H5
						tapX = this.area.height + e.changedTouches[0].pageY
						// #endif
						// #ifndef H5
						tapX = e.changedTouches[0].pageY
						// #endif
					}
					else{
						tapX = e.changedTouches[0].pageX - this.area.left - s[0] / 2 // 拖柄中心与鼠标箭头一致
					}
					tapX = tapX >= 0 ? tapX : 0
					this.handleMove(tapX)
					this.$emit('dragstart', {
						type: 'dragstart',
						value: this.showValue
					})
				}
			},
			areaTouchMove(e) {
				if (!this.disabled){
					let s = this.sizeDeal(this.handleSize)
					let tapX 
					if (this.direction == 'vertical'){
						// #ifdef H5
						tapX = this.area.height + e.changedTouches[0].pageY
						// #endif
						// #ifndef H5
							tapX = e.changedTouches[0].pageY
						// #endif
					}
					else{
						tapX = e.changedTouches[0].pageX - this.area.left - s[0] / 2
					}
					tapX = tapX >= 0 ? tapX : 0
					this.handleMove(tapX)
				}
			},
			touchCancel() {
				if (!this.disabled){
					this.touchEnd()
					this.$emit('dragcancel', {
						type: 'dragcancel',
						value: this.showValue
					})
				}
			},
		}
	}
</script>

<style scoped>
	@import 'cu-progress.css'
</style>
