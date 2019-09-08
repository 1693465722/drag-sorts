# 拖动排序 drag-sorts

#### 可上下左右拖动 支持 H5 app 微信小程序（其他小程序未做测试）

<p align="center"> <img  src='https://img.cdn.aliyun.dcloud.net.cn/stream/plugin_screens/2f5c67e0-a63d-11e9-8af4-0d2fdd21a382_0.png' width='50%'> </p>

####    [ uni-app 插件市场链接 ](https://ext.dcloud.net.cn/plugin?id=582)

* 属性
>
| list | props
--------------- | ----------
渲染数据（数组）  |  对应需要显示的标题

* 事件
>  @change   当发生元素顺序发生变化或删除时触发，参数是改变后的数组其中 sortNumber 是该元素的排序 </br> 删除的元素并没有从数组中真正去掉（因为会影响动画），只是改变了其 isShow 1显示  0隐藏
  
* 基本使用
```
<template>
	<view>
		<view class="title">拖动排序点击删除</view>
		<drag-sort :list="list" :props="props" @change="onDragSortChange" ></drag-sort>
		<view class="footer"><image :src="img" mode="aspectFit"></image></view>
	</view>
</template>
```

```
import dragSorts from '@/components/drag-sorts/index.vue'
export default {
  components: {
    dragSorts
  },
  data () {
    return {
		img:'https://wx2.sinaimg.cn/orj360/005NpmmLly1g4zr1456bkj302r02kwe9.jpg',
      // 对应需要显示的标题
      props: {
        label: 'label'
      },
      list: [
        {
          label: '标题1'
        },
        {
          label: '标题2'
        },
        {
          label: '标题3'
        },
        {
          label: '标题4'
        },
        {
          label: '标题5'
        },
        {
          label: '标题6'
        },
        {
          label: '标题7'
        },
        {
          label: '标题8'
        },
        {
          label: '标题9'
        },
        {
          label: '标题10'
        },
        {
          label: '标题11'
        },
        {
          label: '标题12'
        },
        {
          label: '标题13'
        },
      ]
    }
  },
  methods: {
    onDragSortChange (e) {
      console.log(e)
    }
  }
}
```
```
<style>
	.title{
		text-align: center;
		color: #ccc;
		padding: 20upx;
	}
	.footer{
		display: flex;
		justify-content: center;
		align-items: center;
		height: 100px;
		width: 100%;
	}
	.footer image{
		height: 100%;
		width: 100%;
	}
</style>
```

* 有什么不足和bug大佬们多多指证
   


