# Tips

## 值的单位

- 绝对
  - px 像素
  - pt 磅 (常用于打印)
- 相对
  - em 相对于该元素的font-size
  - rem 相对于html元素的font-size
  - vh 浏览器窗口高度的1%
  - vw 浏览器窗口宽度的1%
  - vmin vh和vw中的较小者
  - vmax vh和vw中的较大者

## HSL颜色模型

- Hue 色相
  - 色彩基本属性, 即颜色
  - 取值0-360
- Saturation 饱和度
  - 色彩的纯度, 约高色彩越纯, 低则变灰
  - 取值0-100%
- Lightness 亮度
  - 越高颜色越亮
  - 取值0-100%;

## 层叠和继承

### 声明的冲突

后一种将覆盖先前的声明:

1. 在用户代理样式表的声明 (浏览器预设)
2. 用户样式表中的普通声明 (用户自定义)
3. 作者样式表中的普通声明 (开发人员)
4. 作者样式表中的重要声明
5. 用户样式表中的重要声明

### 选择器优先级 (特异性, 专用性) Specificity

- 权重

  | 权重 |                      |
  | :--: | -------------------- |
  |  1   | 标签                 |
  |  10  | 伪元素类, 伪类, 属性 |
  | 100  | ID                   |
  | 1000 | 内联样式             |

  > not伪类计算时被当做标签选择器进行计数

### 继承

- 为目标元素直接添加样式, 永远比继承样式的优先级高, 无视优先级的遗传规则

- inherit继承的是父级的计算值 (computed value) 而不是'使用值'

  [CSS求值过程]: http://t.75team.com/video/play?id=36_144_20161229190544211b02f9-8b8f-4425-96cf-196dc5cfe67c	"CSS求值过程 42min"

- 显式继承

  ```
  * {
      box-sizing: inherit;
  }
  
  html {
      box-sizing: border-box;
  }
  
  .some-widget {
      box-sizing: content-box;
  }​
  ```

## 高级选择器

### 伪类

> 元素在 HTML 中真实存在, 满足某些状态时匹配

- :traget 
  - 表示元素被hash匹配时的状态

     比如 URL 是 /post/a#header 时, name为header的元素处于被 traget 的状态

- :lang 

  - 元素匹配上指定语言时的状态
  - 浏览器通过 ``lang`` 属性获得语言信息

- 结构性伪类 (与 HTML 结构相关)

  - :nth-child

    - 通过 :nth-child(an + b) 选中某些子元素

      :nth-child(3n) 选中第3, 6, 9, ... 个子元素

    - n 可以为0

    - a 可以为负数

    - 参考[Master the :nth-child](http://nthmaster.com/)

    - 倒序 :nth-last-child()

  - :nth-of-type

    - 与 :nth-child 的差别在于 **考虑标签类型**
    - 倒序 :nth-last-of-type()

  - :first-child

    - :first-of-type **考虑标签类型**

  - :last-child

    - :last-of-type **考虑标签类型**

  - :not()

    - 排除匹配的元素

      img:not([alt]) 选择没有写 alt 属性的图片

  - :only-child

    - 唯一子元素
    - :only-of-type **考虑标签类型**

  - :empty

    - 标签内为空

### 伪元素 

> 元素在 HTML 中不存在, 在 CSS 中虚拟出来

- ::before
- ::after

- ::first-line
  - 匹配第一行
- ::first-letter
  - 匹配第一个字母

### 弟选择器

> 在 DOM 中, 位于该选择器的后面元素

- 相邻兄弟选择器 E + F
- 通用兄弟选择器 E ~ F
  - :checked ~ F 实现原生switch组件