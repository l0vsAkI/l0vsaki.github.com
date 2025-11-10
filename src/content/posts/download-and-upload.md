---
title: 有关上传与下载
published: 2023-07-20
category: "Frontend"
tags: ["文件传输"]
---

中小型文件的上传与下载业务实现都比较简单, 需要记住的点在于上传与下载在发送的请求中需要做一些
配置, 如上传请求的 `headers `与下载文件流请求的 `responseType`

## 上传

### 单个文件上传

单个小文件上传比较简单, 主要流程为以下三步(伪代码)

1. 将文件插入到 FormData 表单对象中

```ts
let file: File | null = null;
const formData = new FormData();
formData.append("file", file);
```

2. 上传文件的请求头必须为'multipart/form-data'

```ts
// 配置请求头部的headers
request.options.headers["Content-Type"] = "multipart/form-data";
```

3. 将整个插入了文件的 FormData 对象作为请求体的 data

```ts
request.options.data = formData;
```

### 多个文件上传

多文件上传可通过返回一个`Promise`, 通过`Promise.All`静态方法来判定多个文件上传是否完成

## 下载

实现下载业务的后端一般为两种处理方式

- 返回下载链接
- 返回文件流

这里主要说明下文件流的下载

### 文件流下载

请求成功后的响应为文件流, 无法直接使用, 需要前端进行处理.

在下载时需要指定下载文件的[MIME](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Basics_of_HTTP/MIME_types/Common_types)类型

1. 在发送请求之前, 需要设置请求的响应类型

```ts
// blob类型为浏览器专属, axois中默认为json
type ResponseType =
  | ""
  | "arraybuffer"
  | "blob"
  | "document"
  | "json"
  | "text"
  | "ms-stream";
request.responseType = "blob";
```

2. 将返回的文件流放入 Blob 对象中并生成临时链接下载

```ts
/**
 * 下载流文件
 * @param file 二进制流
 * @param fileName 带后缀的文件名
 * @param fileType 文件的MIME类型
 */
export function downloadStreamFile(
  file: BlobPart,
  fileName: string,
  fileType: string,
) {
  const blob = new Blob([file], {
    type: fileType,
  });
  const a: HTMLAnchorElement = document.createElement("a");
  const href = URL.createObjectURL(blob); // 生成临时文件链接
  a.href = href;
  a.download = fileName;
  a.click();
  URL.revokeObjectURL(href); // 一定要在结尾释放内存
}
```

PS: 常用的 MIME 类型

> | 扩展名     | MIME 类型                                                                 |
> | ---------- | ------------------------------------------------------------------------- |
> | .aac       | audio/aac                                                                 |
> | .wav       | audio/wav                                                                 |
> | .mp3       | audio/mpeg                                                                |
> | .avi       | video/x-msvideo                                                           |
> | .webm      | video/webm                                                                |
> | .bmp       | image/bmp                                                                 |
> | .gif       | image/gif                                                                 |
> | .ico       | image/ico                                                                 |
> | .jpg .jpeg | image/jpeg                                                                |
> | .png       | image/png                                                                 |
> | .svg       | image/svg+xml                                                             |
> | .tif .tiff | image/tiff                                                                |
> | .csv       | text/csv                                                                  |
> | .txt       | text/plain                                                                |
> | .doc       | application/msword                                                        |
> | .docx      | application/vnd.openxmlformats-officedocument.wordprocessingml.document   |
> | .ppt       | application/vnd.ms-powerpoint                                             |
> | .pptx      | application/vnd.openxmlformats-officedocument.presentationml.presentation |
> | .xls       | application/vnd.ms-excel                                                  |
> | .xlsx      | application/vnd.openxmlformats-officedocument.spreadsheetml.sheet         |
> | .rar       | application/x-rar-compressed                                              |
> | .7z        | application/x-7z-compressed                                               |
> | .zip       | application/zip                                                           |
> | .pdf       | application/pdf                                                           |
