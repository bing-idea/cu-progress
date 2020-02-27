+ 属性

    |参数|默认值|类型|说明|
    |:---|:---|:---|:---|
    |percent|0|String, Number|设置进度条进度，如50，通过动态设置该值实现进度条进度变化|
    |activeColor|'#444444'|String|已经过区域颜色|
    |noActiveColor|'#888888'|String|为经过区域颜色|
    |strokeWidth|3|String, Number|进度条宽度|
    |animation|false|Boolean|是否使用动画|
    |damping|20|Number|阻尼系数，越大移动越快|
    |handleColor|'#000000'|String|拖柄颜色|
    |handleSize|'8px'|String, Number|拖柄尺寸|
    |handleBorderRadius|'8px'|String, Number|拖柄圆角半径|
    |showInfo|true|Boolean, String|是否在进度条右侧显示百分比|
    |fontSize|'16px'|String, Number|百分比显示字体尺寸|
    |fontColor|'#000000'|String|百分比显示字体颜色|
    |borderRadius|0|String, Number|进度条圆角半径|
    |width|'60vw'|String, Number|进度条总宽，如果显示百分比，则包括百分比在内的宽度
        
        
+ 事件

    |事件|返回参数|说明|
    |:---:|:---:|:---:|
    |change|percent|当前进度条百分比， 如50,进度条进度变化触发|


+ 示例
 ```
 <template>
  <view>
    <view class="show">
      <cu-progress showInfo=false></cu-progress>
      <cu-progress></cu-progress>
      <cu-progress showInfo=false handleSize="16"></cu-progress>
      <cu-progress @change="change_" fontSize="16px" handleSize="16px" fontColor="orange" handleColor="green" activeColor="blue" noActiveColor="yellow"></cu-progress>
    </view>
    <cu-progress @change="change_" width="100vw" percent="100" handleBorderRadius="0" handleColor="orange" handleSize="20" activeColor="red" noActiveColor="black"></cu-progress>
    <cu-progress @change="change_" width="100vw" showInfo="true" percent="100" strokeWidth="50" fontSize="60px"></cu-progress>
    <cu-progress @change="change_" :percent="pct1" width="100vw" handleSize="60" handleBorderRadius="30" showInfo="true" strokeWidth="50" fontSize="60px"></cu-progress>
    <cu-progress @change="change_" :percent="pct2" width="100vw" handleSize="60" handleBorderRadius="30" showInfo="true" strokeWidth="50" fontSize="60px"></cu-progress>
    <cu-progress @change="change_" :percent="pct3" width="100vw" handleSize="60" handleBorderRadius="30" showInfo="true" strokeWidth="50" fontSize="60px"></cu-progress>
  </view>
</template>

<script>
    import cuProgress from '../../components/cu-progress/cu-progress.vue';
    export default {
    components:{
       cuProgress
    },
     data() {
       return {
          pct1: 0,
          pct2: 50,
          pct3: 100,
       };
     },
     methods: {
        change_ (e){
          console.log('home:' + e.percent);
        }
     }
    }
</script>

<style>
  .show {
    display: flex;
    flex-direction: column;
    padding: 30rpx;
  }
</style>
```

+ 示例结果

![结果]('http://baidu.com/....' '结果展示')