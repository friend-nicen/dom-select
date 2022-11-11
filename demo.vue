<template>
  <div class="test" @mousedown="down" @mousemove="move" @mouseup="up">

    <div ref="movable.dom" class="move"></div>

    <template v-for="(item,index) of Array(30)" :key="index">
      <div :ref="(dom)=>push(dom,index)" :class="{selected:selected.indexOf(index) > -1}" class="item">
        我是测试
      </div>
    </template>
  </div>
</template>

<script setup>


/*
* 范围选择，满足以下任意一个条件接口
* 1.
* */


import {reactive, ref} from "vue";

let selected = ref([]); //被选元素的索引
let items = ref([]); //存储所有dom

let push = (dom, index) => {

  items.value[index] = {
    selected: false,
    dom: dom
  }; //记录dom


}


/*
* 初始的原点
* */
let point = {
  /*
  * 鼠标起点坐标
  * */
  start: {
    x: 0, y: 0
  },
  /*
  * 鼠标终点坐标
  * */
  end: {
    x: 0, y: 0
  },
  /*
  * 是否正在move
  * */
  moving: false,
  /*
  * 获取范围内四个角的坐标
  * */
  getPosition() {

    let angle = []; //角

    angle.push(this.start); //起点
    angle.push(this.end); //终点

    angle.push({
      x: this.end.x,
      y: this.start.y
    }); //对角

    angle.push({
      x: this.start.x,
      y: this.end.y
    }); //对角


    return angle;

  },

  /*
  * 判断元素是否满足被选中的条件
  * */
  isSelected(rect) {

    let angle = point.getPosition(); //获取四个角的坐标

    /*
    * 获取指定图形的四个坐标
    * */
    let rectangle = [
      {
        x: rect.x,
        y: rect.y
      },
      {
        x: rect.x + rect.width,
        y: rect.y + rect.height
      },
      {
        x: rect.x + rect.width,
        y: rect.y
      },
      {
        x: rect.x,
        y: rect.y + rect.height
      }
    ];


    /*
    * 互相判断
    * */
    for (let pos of rectangle) {

      if (pos.x > angle[0].x &&
          pos.x < angle[1].x &&
          pos.y > angle[0].y &&
          pos.y < angle[1].y) {

        return true;
      }
    }


    /*
    * 互相判断
    * */
    for (let pos of angle) {


      if (pos.x > rectangle[0].x &&
          pos.x < rectangle[1].x &&
          pos.y > rectangle[0].y &&
          pos.y < rectangle[1].y) {

        return true;
      }
    }


    /*
    * 相交检测（横）
    * */
    if (angle[0].x < rectangle[0].x &&
        angle[1].x > rectangle[1].x &&
        angle[0].y > rectangle[0].y &&
        angle[1].y < rectangle[1].y) {

      return true;
    }


    /*
    * 相交检测（竖）
    * */
    if (angle[0].x > rectangle[0].x &&
        angle[1].x < rectangle[1].x &&
        angle[0].y < rectangle[0].y &&
        angle[1].y > rectangle[1].y) {

      return true;
    }

    return false;
  }

}

/*
* 鼠标按下
* 记录初始点击的位置
* */
let down = (e) => {

  point.start.x = e.x;
  point.start.y = e.y;

  /*
  * 同步选择框的原点
  * */
  movable.left = e.x + "px";
  movable.top = e.y + "px";

  point.moving = true; //标记移动开始
}

/*
* 鼠标移动
* */
let move = (e) => {

  if (!point.moving) return;

  const {x, y} = e; //鼠标位置

  /*
  * 同步放大元素
  * */
  movable.height = Math.abs(point.start.y - y) + "px";
  movable.width = Math.abs(point.start.x - x) + "px";
}

/*
* 鼠标抬起
* */
let up = (e) => {

  /*
  * 没有移动，直接终止
  * */
  if (!point.moving) return;

  movable.width = 0;
  movable.height = 0;
  point.end.x = e.x;
  point.end.y = e.y;
  point.moving = false;

  /*
  * 循环遍历元素是否被选中
  * */
  selected.value = []; //清空被选的索引

  items.value.forEach((item, index) => {
    if (point.isSelected(item.dom.getBoundingClientRect())) {
      selected.value.push(index);
    }
  })

}

/*
* 移动对象的属性
* */
let movable = reactive({
  dom: null,
  top: 0,
  left: 0,
  width: 0,
  height: 0
});


</script>

<style lang="scss" scoped>
.test {
  width: 80vw;

  border: 1px solid #000000;

  display: flex;
  align-items: center;
  flex-wrap: wrap;
  justify-content: center;


  .move {
    position: fixed;
    top: v-bind("movable.top");
    left: v-bind("movable.left");
    width: v-bind("movable.width");
    height: v-bind("movable.height");
    background: rgba(234, 23, 135, 0.3);
  }


  .item {

    display: inline-block;
    padding: 50px;
    margin: 30px;

    background: #0088ff;
    user-select: none;

  }


  .selected {
    background-color: black;
  }

}
</style>
