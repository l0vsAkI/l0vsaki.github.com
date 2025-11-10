---
title: css手抄本
published: 2023-10-13
category: "Frontend"
tags: ["css"]
---

记录些有关 css 使用的技巧和遇到的问题与其解决的方法

## [技巧] 滚动吸附的实现与其实际应用

2024-2-27

项目需求在小程序上展示宫格功能模块, 同时模块实现类似轮播图的横向滑动翻页效果.
效果如下图:

x x x x x | x x x x x
x x x x x
o .

### 实现方案

通过 css 的滚动吸附功能特性来实现, 关键属性为:

1. `scroll-snap-type`
2. `scroll-snap-align`

#### `scroll-snap-type` 配置在包含可滚动项目的容器中

> scroll-snap-type 属性需要知道在哪个方向上有滚动吸附。方向可以是 x、y 或者逻辑对应关系 block、inline。还可以用关键字 both 使两个轴都有滚动吸附。

> 你还可以传入关键字 mandatory 或 proximity。关键字 mandatory 告诉浏览器无论滚动到哪个位置，内容都必须吸附到特定的点，而关键字 proximity 意味着内容可以吸附而不是必须吸附到点上。

#### `scroll-snap-align` 配置在滚动容器中需要对其的元素上

> scroll-snap-align 属性的有效值包括 start、end、center 和 none。这些值用于标示内容应当吸附到滚动容器中的哪个点。

### 代码

```html
<view class="grid-function">
  <view class="grid-item" wx:for="{{15}}" wx:key="index">
    <image class="grid-item__icon" src="{{fileUrl+'/overlay.png'}}"></image>
    <view class="grid-item__text">文化展陈</view>
  </view>
</view>
```

```scss
.grid-function {
  display: grid;
  overflow-x: auto; /* 容器必须为可滚动容器 */
  grid-template-columns: repeat(10, 20%);
  grid-template-rows: repeat(2, 1fr);
  scroll-snap-type: x mandatory; /* 容器配置 */
}

.grid-item {
  display: flex;
  flex-direction: column;
  gap: 8rpx;
  align-items: center;
}

.grid-item:nth-child(5) {
  scroll-snap-align: end; /* 子元素吸附点配置 */
}

.grid-item:nth-child(10) {
  scroll-snap-align: end; /* 5个为1页, 只在第5与第10个元素末尾吸附 */
}
```

## [问题] fixed 定位的容器自身横向滚动内容被裁剪

2023-12-11

基本代码

```tsx
<View className="p-4 flex gap-2 overflow-x-scroll no-scrollbar bg-white">
  {data.map((item) => (
    <View
      className={`py-1 px-3 color-black bg-gray-200 whitespace-nowrap rounded-sm ${
        item.id === typeId ? "activeType" : ""
      }`}
      key={item.id}
    >
      {item.name}
    </View>
  ))}
</View>
```

### 遇到问题

直接将容器调整为 fixed 定位

```tsx
<View className="fixed p-4 flex gap-2 overflow-x-scroll no-scrollbar bg-white">
  {data.map((item) => (
    <View
      className={`py-1 px-3 color-black bg-gray-200 whitespace-nowrap rounded-sm ${
        item.id === typeId ? "activeType" : ""
      }`}
      key={item.id}
    >
      {item.name}
    </View>
  ))}
</View>
```

1. 首先是直接给容器添加 fixed 定位无法水平滚动, 原因是脱离了文档流后无法根据父元素指定高度.
   通过**给容器指定高度**使内部子元素可以横向滚动

```tsx
<View className="fixed w-full p-4 flex gap-2 overflow-x-scroll no-scrollbar bg-white">
  {data.map((item) => (
    <View
      className={`py-1 px-3 color-black bg-gray-200 whitespace-nowrap rounded-sm ${
        item.id === typeId ? "activeType" : ""
      }`}
      key={item.id}
    >
      {item.name}
    </View>
  ))}
</View>
```

2. 容器自身指定有 padding, 导致内部元素尾部内容被裁切.可以**再添加一个容器专门作为 fixed 容器**,
   再来承载原本容器中的内容

   ```tsx
   <View className="fixed w-full">
     <View className="p-4 flex gap-2 overflow-x-scroll no-scrollbar bg-white">
       {dataOfInformationTypeList?.data.map((item) => (
         <View
           className={`py-1 px-3 color-black bg-gray-200 whitespace-nowrap rounded-sm ${
             item.id === typeId ? "activeType" : ""
           }`}
           key={item.id}
           onClick={() => handleSelectType(item.id)}
         >
           {item.name}
         </View>
       ))}
     </View>
   </View>
   ```

## [问题] margin 塌陷

2023-10-19

还是在写代表直接得时候, 遇到了 margin 高度塌陷的问题, 父子元素共用了各个方向最大的 margin 值.

### 解决问题

1. 使用`padding`或者`border`

```css
.parent {
  padding: 1px;
}
```

```css
.parent {
  border: 1px solid transparent;
}
```

2. BFC
   通过触发元素的[BFC(Block Formatting Context)](https://developer.mozilla.org/zh-CN/docs/Web/Guide/CSS/Block_formatting_context)使用 BFC 语法规则来移除 margin 塌陷.

有以下几种常用的处理方式:

| 属性     | 值                                                |
| -------- | ------------------------------------------------- |
| float    | left / right                                      |
| overflow | hidden / scroll / auto                            |
| position | absolute / fixed                                  |
| display  | inline-block / tabel / tabel-cell / tabel-catioon |

3. 使用Flexbox或者Grid布局
   将父元素的布局方式改为`flex`或者`grid`

4. 使用伪元素
   推荐一种不需要使用`before`伪元素的时候的解决方法

```css
.parent::before {
  content: "";
  display: tabel;
}
```

## [问题] relative 父元素的 absolute 子元素导致的层级错误

2023-10-13

写代表之家的时候, 需要给一个 _card_ 添加背景图片.

采用的方案是在文件服务器上传图片, 通过`<img>`标签的绝对定位将*bg-img*
作为 _card_ 的背景图片, _card_ 的内容为 _desc_ .

### 遇到问题

_bg-img_ 用 absolute 定位在父元素*card*上, 显示正常. 但是正常定位并且 z-index 大于
_bg-img_ 的 _desc_ 中的文本内容会被 _bg-img_ 遮挡, 而`<img>`标签会正常显示.

```scss
.card {
  position: relative;
  z-index: 0;

  .bg-img {
    position: absolute;
    top: 0;
    left: 0;
    z-index: 0;
  }

  .desc {
    z-index: 1;
  }
}
```

### 解析问题

_desc_ 是 static 定位, 与其他指定了定位的元素不是同一大的层级, z-index 无效.

### 处理问题

给 _desc_ 添加定位`relative`, 可完成正常的层级展示.

> 这个其实是很基础的问题, 以前应该也遇到过, 但是没有记录下来,
> 开发的时候再次遇见这个问题却完全没有了印象.

---

## [技巧] 保持图片纵横比

基础布局

```html
<article class="card" style="width: 300px">
  <div class="ratable-img">
    <img src="image/9.jfif" alt="" />
  </div>
</article>
```

### 旧的实现

```scss
.ratable-img {
  position: relative;
  padding-top: 75%;

  img {
    position: absolute;
    left: 0;
    top: 0;
    width: 100%;
    height: 100%;
    object-fit: cover;
  }
}
```

### 新的实现

```scss
.ratable-img {
  aspect-ratio: 4/3;
  width: 400px;

  img {
    width: 100%;
    height: 100%;
  }
}
```

---

## [技巧] 防止滚动条抖动

```css
.elem {
  padding-right: calc(100vw - 100%);
}
```

---

## [技巧] 单行文本省略

```css
.ellipsis-line-1 {
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}
```

---

## [技巧] 多行文本省略

```css
.ellipsis-line-2 {
  display: -webkit-box;
  -webkit-box-orient: vertical;
  -webkit-line-clamp: 2; // 第几行的末尾省略
  overflow: hidden;
  word-break: break-all;
  text-overflow: ellipsis;
}
```

---

## [技巧] 伪元素装饰线

```scss
.parent {
  &::before {
    content: "";
    display: inline-block; // 需要指定为block, 非block无内容不显示
    vertical-align: middle; // 调整伪元素的中部对准(父元素的基线加上父元素小写字母x的height的一半)
    width: 4px;
    height: 20px;
    background: #1482f0;
  }
}
```
