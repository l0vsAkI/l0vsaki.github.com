---
title: 给Vue2老项目添加翻页预览pdf
published: 2023-10-24
category: "Frontend"
tags: ["vue", "pdf"]
---

接到任务, 将 3 年前基于 Vue2 搭建的项目焕新, 同时新增需求:
**在管理端可以预览/下载代表的 pdf 文件**

## 实现方案

1. 向后端发送请求, 返回一个数据流
2. 前端正确的接受数据流后拿到 pdf 文件
3. 将 pdf 文件转为图片
4. 用图片实现翻页预览的效果

## 准备工作

经过了多次的踩坑与试错, 最后是通过两个包分别实现 pdf 转图片和翻页预览

- pdfjs-dist - Mozilla 维护的 pdf 包, 有很多强大的功能
- flipbook-vue - 适用于 Vue 的翻页预览组件

_注意!_

> pdfjs-dist 在 2.7.570 版本后使用了 es2020 的语法, 而 Vue2 并不支持, 需指定包
> 版本号为 2.7.570

## 开发历程

项目中使用 Axios 做请求收发, Axios 默认的`responseType`为`json`, 需要另行发起一个请求且`responseType`为`blob`

```js
this.canOpenPdf = false;
axios({
  method: "get",
  url: process.env.VUE_APP_BASE_URL + "/api/manager/member/generatePdf",
  params: {
    uid: row.uid,
  },
  headers: {
    Authorization: getToken(),
  },
  responseType: "blob", // 指定responseType
})
  .then((res) => {
    const pdfBlob = res.data;
    this.pdfUrl = URL.createObjectURL(pdfBlob);
  })
  .finally(() => {
    this.canOpenPdf = true;
  });
```

获取到 pdf 的文件临时地址后将其作为 prop 传递给用来展示 pdf 的组件

```vue
<template>
  <pdf-viewer :pdfUrl="pdfUrl" @closeViewer="pdfUrl = ''" />
</template>

<script>
import PdfViewer from "@/components/PdfViewer";

export default {
  components: { PdfViewer },
};
</script>
```

### 编写 pdf 预览组件

```vue
<template>
  <Flipbook
    v-if="isLoading"
    ref="flipbook"
    class="flipbook"
    :pages="pages"
    :startPage="pageNum"
  ></Flipbook>
</template>

<script>
// 直接引入flipbook.js失败报错, 换用.cjs引用
import Flipbook from "flipbook-vue/dist/vue2/flipbook.cjs";

import * as pdfjs from "pdfjs-dist/es5/build/pdf";
import * as pdfjsWorker from "pdfjs-dist/es5/build/pdf.worker";
pdfjs.workerSrc = pdfjsWorker; // 指定pdfjs使用的worker

export default {
  props: ["pdfUrl"],
  components: { Flipbook },
  data() {
    return {
      pages: [], // 翻页组件用来展示的图片
      pageNum: null, // 首次渲染展示第几页
      pageCount: null, // 总共页数
      imgArr: [], // pdf转图片的存储数组
    };
  },
  watch: {
    // 监听pdfUrl的变化, 不为空时才执行初始化操作
    pdfUrl: function (newValue) {
      if (!newValue) return;
      this.init(newValue);
    },
  },
  methods: {
    init(blobUrl) {
      // 启动pdfjs的加载任务
      const loadingTask = pdfjs.getDocument(blobUrl);

      // pdfjs必须通过Promise来处理, 不支持Promise的浏览器无法使用
      loadingTask.promise.then((pdf) => {
        this.pageCount = pdf.numPages;
        for (let i = 0; i < pdf.numPages; i++) {
          // 遍历加载后的pdf的每一页
          pdf.getPage(i + 1).then((page) => {
            const viewport = page.getViewport(1);
            const canvas = document.createElement("canvas");
            const context = canvas.getContext("2d");
            canvas.height = viewport.height;
            canvas.width = viewport.width;
            // 渲染参数
            const renderContext = {
              canvasContext: context,
              viewport,
            };

            // 将本页渲染至canvas画布
            const renderTask = page.render(renderContext);
            renderTask.then(() => {
              // 通过canvas的api生成图片
              const imgUrl = canvas.toDataURL("image/jpeg");
              this.imgArr[i] = imgUrl;
              if (this.imgArr.length === this.pageCount) {
                // 将用来展示的图片数组前推入null用来单页显示
                this.pages = [null, ...this.imgArr];
                // 数据初始化完成后在渲染组件
                this.isLoading = false;
              }
            });
          });
        }
      });
    },
  },
};
</script>
```

使用组件只需引入后传入 pdf 文件请求完成后在浏览器中的临时文件地址即可

end.
