# JS如何实现网页上通过鼠标移动批量选择元素？

简单说就是类似电脑桌面，通过鼠标选择多个图标的那种效果。如下：

![image](https://user-images.githubusercontent.com/84662035/201392302-efad59cf-954d-4c68-83fb-f8cbd7338550.png)

在线查看：[https://nicen.cn/collect/demo](https://nicen.cn/collect/demo/)
 
## 基本思路

监测外部容器的mousedown、mousemove、mouseup事件来进行选择判断，大致dom结构如下：

```
<div class="test">
    <!--鼠标移动时显示的选择框-->
    <div class="move"></div>
    <!-- 待选项 -->
    <div class="item">
      我是测试
    </div>
    <div class="item">
      我是测试
    </div>
    <div class="item">
      我是测试
    </div>
    ....
</div>
```

大致实现过程：

1. 鼠标按下，将选择框的位置（top、left）设置为点击位置，选择框初始宽高为0。
2. 鼠标移动，将选择框的大小（height、width）设置为鼠标移动的距离（起始点和终点的差）
3. 鼠标抬起，停止选择框大小跟随鼠标移动，计算与选择框发生重叠的元素。

## 情形分析

网页上的元素重叠，存在多种不同的情况，针对每一种情况有不同的检测方法。

### 1.角重叠

角重叠，也就是选择框有至少一个角在元素的范围内，或者元素至少有一个角在选择框的范围内，此时可判断元素被选中。

![image](https://user-images.githubusercontent.com/84662035/201391854-0b227670-d51d-4c12-a7df-ac6f17fdcc8f.png)

可以通过对选择框和元素进行相互检测，来判断元素是否选中，如图，判断一个点是否在方形内的算法如下：

![image](https://user-images.githubusercontent.com/84662035/201391925-89c6fbfc-c54a-4f87-96e8-b20ea63a1da2.png)

```
//简单的判断
if ( 
     X > X1 &&  X < X2 &&
     Y > Y1 && Y < Y2
) {
        return true;
 }
```

通过以上算法循环判断选择框的四个坐标点是否在元素内，然后再判断元素的四个角是否在选择框内，只要存在一个True，元素就被选中。

### 2.相交重叠

相交重叠不存在角重叠的情况，需要通过坐标范围进行判断。

![image](https://user-images.githubusercontent.com/84662035/201391467-d19ea4b8-1256-44a7-8a2c-367efea00ad1.png)

相对应的算法可以解释为 x3 < x1  && x2 < x4 && Y3 > Y1 && Y4 < Y2（纵向相交算法同理），两个图形的坐标反过来即可。

## 测试代码

用vue写的例子，实现了上述的算法，没有做具体的完善，仅作为参考。

Github：[https://github.com/friend-nicen/dom-select](https://github.com/friend-nicen/dom-select)

Gitee：[https://gitee.com/friend-nicen/dom-select](https://gitee.com/friend-nicen/dom-select)

在线查看：[https://nicen.cn/collect/demo](https://nicen.cn/collect/demo)
