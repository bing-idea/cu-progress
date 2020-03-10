**uni-app 自定义进度条组件**

*movable-area + movable-view + css 模拟进度条，拖动条，相比于官方progress和slider功能更完善，可自定义程度更高*

![image](https://raw.githubusercontent.com/18212297551/cu-progress/master/6.gif)
+ 属性

    |参数|默认值|类型|说明|
    |:---|:---|:---|:---|
    |value|0|Integer|设置进度条进度，如50，通过动态设置该值实现进度条进度变化。parseInt格式化，异常默认min|
    |max|100|Integer|最大值，parseInt格式化，异常默认100|
    |min|0|Integer|最小值，parseInt格式化，异常默认0|
    |activeColor|#444444|String|已经过区域颜色|
    |noActiveColor|#888888|String|未经过区域颜色|
    |strokeWidth|3|String, Number|进度条宽度|
    |width|200px|String, Number|进度条总宽，如果showinf0为true，则包括文字显示在内的宽度|
    |showInfo|true|Boolean|是否在进度条右侧显示百分比|
    |infoEndText|null|String|进度显示后缀，在原有数字进度的基础上，在其后添加字符|
    |infoSize|16px|String, Number|百分比显示字体尺寸|
    |infoColor|#000000|String|百分比显示字体颜色|
    |borderRadius|0|String, Number|进度条圆角半径|
    |backgroundColor|inherit|String|整个组件的背景颜色|
    |bgBorderRadius|0|String, Number|整个组件的border-radius|
    |handleColor|#000000|String|拖柄颜色|
    |handleSize|8px|String, Number|拖柄尺寸|
    |handleBorderRadius|8px|String, Number|拖柄圆角半径|
    |handleIcon|null|String|通过class属性为拖柄设置字体图标|
    |iconSize|8px|Number, String|拖柄图标大小|
    |iconColor|inherit|String|拖柄图标颜色|
    |iconBorderRadius|8px|String|拖柄图标圆角半径|
    |disable|false|Boolean|是否禁止拖动|
    |damping|100|Number|阻尼系数，越大移动越快|
    |direction|horizontal|String|进度条方向，horizontal or vertical, 注意：设置为vertical时，info相关属性设置无效，不能够显示当前进度值|
    |infoAlign|right|String|进度显示位置，值 right center left，注意：当为center时，当拖柄移动到info显示区域，info会被部分遮盖，如不想被遮盖可将handleSize设置为0，去除拖柄|
    |step|1|Number|步长，可以为小数，它将决定进度值所保留的小数位数，步长有多少位有效小数（末尾为0不计），进度值就最多保留几位小数注意step将影响min和max值，如 min=0.1, step=0.5, max=10,那么进度的最大值只能达到9.6,因此若要完全从设置的min值变化到max,步长应该被max-min整除；如果 min=0.1, step=1, 那么最小值会是0|

    
        
+ 事件

    |事件|返回参数|说明|
    |:---:|:---:|:---:|
    |change|type:change, value:进度条百分比|进度条进度变化触发|
    |dragstart|type:dragstart, value:进度条百分比|手指接触进度条触发|
    |dragging|type:dragging, value:进度条百分比|拖拽进度条触发|
    |dragged|type:dragged, value:进度条百分比|拖拽完成手指离开屏幕触发|
    |dragcancel|type:dragcancel, value:进度条百分比|拖拽取消触发，如手机来电导致拖拽终止|

