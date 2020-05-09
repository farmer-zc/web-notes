# flex布局（弹性布局）

```
// 任意一个容器都可以指定为弹性布局
.box { display: flex; }
```

## 容器的属性

```CSS
1. flex-direction: row;           /*项目的排列方向（主方向）*/
2. flex-wrap: nowrap;             /*项目一行排不下的表现形式*/
3. flex-flow: row nowrap;         /*flex-direction 与 flex-wrap 的简写*/
4. justufy-content:flex-start;    /*定义项目在主方向的对其方式*/
5. align-items: flex-start;       /*定义项目交叉方向的对齐方式*/
6. align-content: flex-start;	  /*定义了多根轴线的对齐方式*/
```



1. flex-direction：项目的排列方向（主方向）

   ```
   row（默认值）：从左到右
   row-reverse：从右到左
   column：从上到下
   column-reverse: 从下到上
   ```

2. flex-wrap：一条线排不下的表现形式

   ```
   nowrap（默认值）：不换行
   wrap：换行，第一行在上面
   wrap-reverse：换行第一行在下面
   ```

3. flex-flow：flex-direction 与 flex-wrap 的简写，默认值 row nowrap

4. justufy-content：定义项目在主方向的对其方式

   ```
   flex-start（默认值）：左对齐
   flex-end：右对齐
   center：居中
   space-between: 两端对齐，中间均等
   space-around：每项之间两侧间隔相等
   ```

5. align-items：定义项目交叉方向的对齐方式

   ```
   flex-start: 交叉轴起点对齐
   flex-end: 交叉轴终点对齐
   center: 居中
   baseline: 项目的第一行文字对齐
   stretch：如果项目没有设置高度或者为auto，将占满整个容器
   ```

6. align-content: 定义了多根轴线的对齐方式

   ```
   flex-start: 交叉轴起点对齐
   flex-end: 交叉轴终点对齐
   center: 居中
   space-between: 与交叉轴两端对齐，轴线之间的间隔平均分布
   space-around：每根轴线两侧的间隔都相等
   stretch：占满整个交叉轴
   ```

## 项目上的属性

1. order：项目的排列顺序，数值越小月靠前，默认为0 
2. flex-grow：定义项目的放大比例，默认为0 不放大
3. flex-shrink：属性定义了项目的缩小比例，默认为1，即如果空间不足，该项目将缩小。
4. flex-basis：属性定义了项目的缩小比例，默认为1，即如果空间不足，该项目将缩小。
5. flex： 是`flex-grow`, `flex-shrink` 和 `flex-basis`的简写，默认值为`0 1 auto`。后两个属性可选。
6. align-self：单个项目的对齐方式，可覆盖`align-items`属性。默认值为`auto`

