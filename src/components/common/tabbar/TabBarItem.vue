<!-- TabBarItem -->
<template>
<div class='tab-bar-item' @click="itemClick">
    <slot v-if="isActive" name="item-icon"></slot>
    <slot v-else name="item-icon-active"></slot>
    <div :style="activeStyle"><slot  name="item-text"></slot></div>

</div>
</template>

<script>
//这里可以导入其他文件（比如：组件，工具js，第三方插件js，json文件，图片文件等等）
//例如：import 《组件名称》 from '《组件路径》';

export default {

props: {
  path: String,
  activeColor:{
    type: String,
    default: 'blue'
  }
},
data() {
//这里存放数据
return {
 // isActive: false

};
},
//监听属性 类似于data概念
computed: {
  isActive(){
    return this.$route.path.indexOf(this.path) !== -1
  },
  activeStyle(){
    return this.isActive ? {color: this.activeColor} : {}
  }
},

//方法集合
methods: {
  itemClick(){
    this.$router.replace(this.path)
  }

}

}
</script>

<style>
.tab-bar-item{
    flex: 1;
    text-align: center;
    height: 49px;
    font-size: 14px;
  }
  .tab-bar-item img{
    width: 24px;
    height: 24px;
    margin-top:3px;
    vertical-align: middle;
    margin-bottom: 2px;
  }

</style>