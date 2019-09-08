<template>
	<view class="">
		  <movable-area class="drag-sort" :style="{height:boxHeight }" id="drag">
			<movable-view
			v-for="(item, index) in currentList"
			:key="index"
			:x="item.x"
			v-if="item.isShow === 1"
			:data-index="index"
			@touchstart="touchstart"
			@touchmove.stop="touchmove"
			@touchend="touchend"
			@click="close(index)"
			:y="item.y"
			:direction="direction"
			disabled
			damping="40"
			:animation="item.animation"
			class="drag-sort-item"
			:style="styleObject"
			:class="{'active': active == index, 'vh-1px-t': item.index > 0}">
			  <view class="item">
				  <text>{{item[props.label]}}</text>
			  </view>
			</movable-view>
		  </movable-area>
	</view>

</template>

<script>
export default {
  name: 'drag-sort',
  mixins: [],
  components: {},
  data () {
    return {
	  styleObject: {
		color: 'red',
		fontSize: '13px'
	  },
		style:{
			background:'red'
		},
	  direction:"all",
	  windowWidth:100, //屏幕宽度
      height: 40, // 高度
      currentList: [],
      active: -1, // 当前激活的item
      index: 0, // 当前激活的item的原index
      topY: 0, // 距离顶部的距离
	  topX: 0, // 距离左侧偏移位置
	  deviationX: 0,
      deviationY: 0// 偏移量
    }
  },
  computed: {
	  boxHeight(){
		  return (Math.ceil((Number(this.list.length)+1)/4)) * this.height + 'px'  
	  }
  },
  props: {
    list: {
      type: Array,
      default: () => {
        return []
      }
    },
    boxStyle: {
      type: Object,
      default: () => {
        return {}
      }
    },
    props: {
      type: Object,
      default: () => {
        return {
          label: 'label',
          value: 'value'
        }
      }
    }
  },
  watch: {
    list:{
		handler(){
			debugger
			this.onUpdateCurrentList()
		},
		deep:true
      
    }
  },
  created () {
    const res = uni.getSystemInfoSync();
	this.windowWidth = res.windowWidth
    this.onUpdateCurrentList()
  },
  mounted () {
  },
  updated () {},
  filters: {},
  methods: {
    onUpdateCurrentList (list = this.list) {
      let arr = []
      for (const key in list) {
		  // console.log(key)
		  let height =  Math.ceil((Number(key)+1)/4) - 1
		  let x = 0
		  if(key <= 3){
			  x = key * this.windowWidth * 0.24 + this.windowWidth * 0.04 || this.windowWidth * 0.04
		  }else{
			  if((Number(key)+1)%4 === 0){
				   x = 3 * this.windowWidth * 0.24 + this.windowWidth * 0.04 || this.windowWidth * 0.04
			  }else{
				  x = ((Number(key)+1)%4-1) * this.windowWidth * 0.24 + this.windowWidth * 0.04 || this.windowWidth * 0.04
			  }
			  
		  }
        arr.push({
          ...list[key],
		  isShow:1,
          index: Number(key),
		  SortNumber:Number(key),
          y: height * this.height,
		  x,
          animation: true
        })
      }
      this.currentList = arr
    },
	// 根据排序进行重新计算位置
	moveUpdateCurrentList(index){
      for (const i in this.currentList) {
		  let key
		  if(this.currentList[i].SortNumber || this.currentList[i].SortNumber === 0){
			  key = this.currentList[i].SortNumber
		  }else{
			  key = Number(i)
		  }
		  // console.log(key)
		  let temobj = { ...this.currentList[i] }
		   // debugger
		  this.currentList[i].y =  (Math.ceil((Number(key)+1)/4) - 1)*this.height 
		  if(index == key){
			  continue
		  }else{
			  if(key <= 3){
				  this.currentList[i].x = key * this.windowWidth * 0.24 + this.windowWidth * 0.04 || this.windowWidth * 0.04
			  }else{
				  if((Number(key)+1)%4 === 0){
					   this.currentList[i].x = 3 * this.windowWidth * 0.24 + this.windowWidth * 0.04 || this.windowWidth * 0.04
				  }else{
					   this.currentList[i].x = ((Number(key)+1)%4-1) * this.windowWidth * 0.24 + this.windowWidth * 0.04 || this.windowWidth * 0.04
				  }
			  } 
		  }
      }
	  this.$emit('change', this.currentList)
	},
    touchstart (e) {
      // 计算 x y 轴点击位置
      var query = uni.createSelectorQuery().in(this)
      query.select('#drag').boundingClientRect()
      query.exec((res) => {
        this.topY = res[0].top
		this.topX  = res[0].left
        let touchY = e.mp.touches[0].clientY - res[0].top
        let touchX = e.mp.touches[0].clientX - res[0].left
        this.deviationY = touchY % this.height
		 this.deviationX = touchX % (this.windowWidth*0.2)
		this.active = Number(e.currentTarget.dataset.index)
		this.index = Number(e.currentTarget.dataset.index)
      })
    },
    touchmove (e) {
      if (this.active < 0) return
	  let temY = e.mp.touches[0].clientY - this.topY
	  let temX = e.mp.touches[0].clientX - this.topX
      let touchY = temY - 15
      let touchX = temX - this.windowWidth*0.1
      this.currentList[this.active].y = touchY 
      this.currentList[this.active].x = touchX
      this.currentList[this.active].animation = false
	  this.currentList.every( (res, index) => {
			let absX =  Math.abs(touchX - res.x)
			let absY =  Math.abs(touchY - res.y)
			// 设置元素定点距离多少进行重排
		  if( 0 < absX && absX <= 10 && absY > 0 && absY <= 10 &&　this.active　!=　index){
			  // debugger
			  let temNumber = this.currentList[index].SortNumber
			  this.currentList.every( (_res, _index) => {
				  // 判断从大像小移还是从小向大移
				  if( this.currentList[this.active].SortNumber < this.currentList[index].SortNumber){
					  // 移动元素比目标元素所在位置小，之间元素排序--
						if( this.currentList[_index].SortNumber > this.currentList[this.active].SortNumber && this.currentList[_index].SortNumber <= this.currentList[index].SortNumber){
							  _res.SortNumber--
						  }
				  }else{
					  // 反之++
						if( this.currentList[_index].SortNumber < this.currentList[this.active].SortNumber && this.currentList[_index].SortNumber >= this.currentList[index].SortNumber){
							  _res.SortNumber++
						  }
				  }
				  return true
			  }, this)
			   this.currentList[this.active].SortNumber = temNumber 
			  this.moveUpdateCurrentList(temNumber)
			  return false
		  }else{
			  return true
		  }
	  }, this)
    },
    touchend (e) {
	  if(this.currentList[this.active]){
		   this.currentList[this.active].animation = true
	  }
	   this.moveUpdateCurrentList(-1)
      this.active = -1
    },
	// 关闭按钮
	close(index){
		// debugger
		uni.showToast({
			title:'点击删除'
		})
		console.log("我是删除的index 的SortNumber")
		console.log(this.currentList[index].SortNumber)
		this.currentList.forEach( (item, i) => {
			if(this.currentList[i].SortNumber > this.currentList[index].SortNumber){
				item.SortNumber--
			}
		})
		
		this.moveUpdateCurrentList(-1)
		this.currentList[index].isShow = 0 

	}
  }
}
</script>

<style lang='less' scoped>
@import "~./1px.less";
.drag-sort {
  width: 100%;
}
.drag-sort-item {
  position: absolute !important;
  display: flex;
  height: 30px;
  align-items: center;
  width: 20%;
  text-align: center;
  background-color: #007aff;
  color: #fff;
  /* border-radius: 5px; */
  box-sizing: border-box;
  .item {
	position: relative;
    flex: 1;
	font-size: 16px;
  }
  .close{
	  position: absolute;
	  right: -10upx;
	  top: -10upx;
	  width: 30upx;
	  z-index: 99;
	  height: 30upx;
	  opacity: 0.8;
  }
  .close image{
	  width: 100%;
	  height: 100%;
  }
  .touch-tight {
    width: 24px;
    display: flex;
    justify-content: center;
  }
}
.touch {
  height: 100%;
  width: 50px;
}
.active {
  box-shadow: 0 0 40rpx #DDDDDD;
  z-index: 99;
}
</style>
