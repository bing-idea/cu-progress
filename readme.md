**uni-app 自定义进度条组件**
+ 属性

    |参数|默认值|类型|说明|
    |:---|:---|:---|:---|
    |value|0|Integer|设置进度条进度，如50，通过动态设置该值实现进度条进度变化。parseInt格式化，异常默认min|
    |max|100|Integer|最大值，parseInt格式化，异常默认100|
    |min|0|Integer|最小值，parseInt格式化，异常默认0|
    |activeColor|#444444|String|已经过区域颜色|
    |noActiveColor|#888888|String|未经过区域颜色|
    |strokeWidth|3|String, Number|进度条宽度|
    |width|200px|String, Number|进度条总宽，如果显示百分比，则包括百分比在内的宽度|
    |showInfo|true|Boolean, String|是否在进度条右侧显示百分比|
    |infoEndText|null|String|进度显示后缀，在原有数字进度的基础上，在其后添加字符|
    |fontSize|16px|String, Number|百分比显示字体尺寸|
    |fontColor|#000000|String|百分比显示字体颜色|
    |borderRadius|0|String, Number|进度条圆角半径，仅微信小程序，progress组件本身支持限制|
    |backgroundColor|inherit|String|整个组件的背景颜色|
    |bgBorderRadius|0|String, Number|整个组件的border-radius|
    |handleColor|#000000|String|拖柄颜色|
    |handleSize|8px|String, Number|拖柄尺寸|
    |handleBorderRadius|8px|String, Number|拖柄圆角半径|
    |handleIcon|null|String|通过class属性为拖柄设置字体图标|
    |iconSize|8px|Number, String|拖柄图标大小|
    |iconColor|inherit|String|拖柄图标颜色|
    |iconBorderRadius|8px|String|拖柄图标圆角半径|
    |damping|100|Number|阻尼系数，越大移动越快|
    
        
        
+ 事件

    |事件|返回参数|说明|
    |:---:|:---:|:---:|
    |change|value|当前进度条百分比， 如50,进度条进度变化触发|
    |dragging|value|当前进度条百分比， 如50,拖拽进度条触发|
    |gragged|value|当前进度条百分比， 如50,拖拽完成手指离开屏幕触发|
    |touchstart|value|当前进度条百分比， 如50,手指触摸进度条触发|
    |touchmove|value|当前进度条百分比， 如50,手指拖动进度条触发|
    |touchend|value|当前进度条百分比， 如50,手指触摸进度条离开屏幕后触发|

