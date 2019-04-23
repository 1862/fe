# CSS3常用特性

1. 文本溢出

   ```css
   overflow: hidden;
   text-overflow:ellipsis;
   white-space: nowrap;
   ```

2. background

   - background-size
     - contain; //缩小图片以适合元素
     - cover; //扩展元素以填补元素
   - background-position

3. transform
   - translate(), 移动
   - rotate(), 旋转
   - scale(), 尺寸缩放
   - skew(), 倾斜
   - matrix(), 2d变换矩阵
   - matrix3d(); 3d变换矩阵
4. transition 时间过渡效果

5. animation 动画效果

   ```css
   /* 定义 */
   @keyframes  anim1 {
     from{left:0px;background-color:red;}
     to {left:250px;}
   }
   
   @keyframes mymove {
     0%   {left: 0px;}
     25%  {left: 200px;}
     50%  {left: 100px;}
     75%  {left: 200px;}
     100% {left: 0px;}
   }
   
   /* 调用 */
   animation: anim1 3s ease-out 1s infinite alternate forwards;
   ```

6. flex 布局

7. box-shadow / text-shadow 阴影

   ```css
   对象选择器 { box-shadow:投影方式 X轴偏移量 Y轴偏移量 阴影模糊半径 阴影扩展半径 阴影颜色 }
   
   div { box-shadow:inset x-offset y-offset blur-radius spread-radius color }
   ```

8. @font-face

   ```css
   @font-face {
       font-family: myFirstFont;
       src: url('Sansation_Light.ttf'),
            url('Sansation_Light.eot'); /* IE9+ */
   }
   
   div{
     font-family: myFirstFont;
   }
   ```

9. 渐变

   ```css
   /* 线性渐变 */
   background: linear-gradient(to bottom, #fff 0%, red 50%, #000 100%)
   
   /* 径向渐变 */
   background: radial-gradient(ellipse 50px 30px at 50% 50% , #fff 0%, red 30%, #000 100%)
   ```

10. 