---
title: SVG边框
published: 2024-06-14
category: "Frontend"
tags: ["css", "svg"]
---

开发 UI 的时候有一个需求, 需要给一个元素同时应用渐变背景色与渐变边框, 同时还有圆角.

设计稿生成代码如下:

```css
background: linear-gradient(
  84deg,
  rgba(40, 204, 226, 0.24) 0%,
  rgba(55, 53, 202, 0.44) 100%
);
border: 1.6px solid;
border-image: linear-gradient(
    85deg,
    #3a11c3 0%,
    #2ad6ec 33%,
    #2ad6ec 71%,
    #3a11c3 100%
  )
  1.600000023841858 1.600000023841858;
border-radius: 4px;
```

## 问题

`border-radius`与`border-image`冲突

同一元素上, 在配置了`border-image`属性后, `border-radius`将不会再生效.

## 思路

1. 最简单的解决方案是在元素下方定位一个 div,遮挡住中间的背景呈现为视觉上的渐变边框, 但是`background-image`属性不仅是渐变的渐变, 还有不透明度的渐变, 故无法用此方案解决.
2. 通过`canvas`绘图实现需要调用相关 api, 不够轻量.

## 方案

网上了解到了使用 SVG 结合 CSS 来绘制边框的方法来实现此需求.

SVG 非常擅长绘制渐变, 再结合 CSS 来实现布局自适应内容.

- 封装一个容器组件, 根节点使用`relative`定位, 盒大小设置为`inline-block`
- 绘制 SVG 边框, 使用`absolute`定位
- 子组件自行配置渐变背景, 再将子组件传入此容器组件

```tsx
interface Props {
  children: JSX.Element;
}

export default function BorderWarpper({ children }: Props) {
  return (
    <div className="relative inline-block overflow-hidden">
      <svg width="100%" height="100%" className="absolute ">
        <rect
          x="1"
          y="1"
          width="100%"
          height="100%"
          style={{
            width: "calc(100% - 2px)",
            height: "calc(100% - 2px)",
          }}
          rx="4px"
          stroke="url(#gradientBorder)"
          strokeWidth="2"
          strokeLinecap="round"
          fill="none"
        />
        <defs>
          <linearGradient id="gradientBorder" x1="0" y1="1" x2="1" y2="0">
            <stop stopColor="#3A11C3" />
            <stop offset="33%" stopColor="#2AD6EC" />
            <stop offset="66%" stopColor="#2AD6EC" />
            <stop offset="1" stopColor="#3A11C3" />
          </linearGradient>
        </defs>
      </svg>
      {children}
    </div>
  );
}
```

## 注意

SVG 有自身的特性需要单独处理:

1. 描边是居中描边, 不可修改
2. 默认超出隐藏, 即自带`overflow-hidden`

故一边情况下, 只能看见一半宽度的 SVG 边框, 1px 边框实际展示效果为 0.5px.

解决方案已在上方组件代码中, 通过 CSS 的`calc`函数来计算 SVG 的大小, 同时调整图形的起始坐标, 留出边框展示的位置.

 
