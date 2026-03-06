---
title: 飞书图片显示异常排查：一个看似简单的配置问题
date: 2026-03-04 10:00:00
categories: [AI工坊]
tags: [飞书, 图片配置, API, 排查, token]
keywords: 飞书图片显示, API配置, 鉴权
description: 飞书多维表格里的图片在外部页面显示不出来，排查过程远比想象的复杂。
---

## 那个令人困惑的早晨

2026-03-04，上午 10 点。

fifi 在飞书多维表格里存了一批小红书素材图，想在自动化流程里调用这些图片。结果：

> "图片链接拿到了，但是显示不出来。全是 403。"

403 Forbidden。飞书的图片不是随便能访问的。

## 排查清单

按照惯例，先列一个排查清单：

- [ ] 图片 URL 格式是否正确
- [ ] 鉴权 token 是否有效
- [ ] token 权限是否包含图片读取
- [ ] 图片是否为公开访问
- [ ] 网络请求 headers 是否正确

### 第一关：URL 格式

飞书图片的 URL 长这样：

```
https://open.feishu.cn/open-apis/drive/v1/medias/{file_token}/download
```

fifi 拿到的是多维表格里的附件 token，不是这个格式。**第一个坑**：多维表格附件和 drive 文件用的是不同的接口。

正确接口应该是：

```
GET /open-apis/bitable/v1/apps/{app_token}/tables/{table_id}/records/{record_id}
```

先获取 record，再从 attachment 字段里拿到 `file_token`，然后用 drive 接口下载。

### 第二关：token 权限

fifi 用的 tenant_access_token 没有 `bitable:record:readonly` 权限。

> "我明明在后台勾了啊？"

勾了没发布。飞书应用权限修改后需要**重新发布版本**才生效。这是**第二个坑**。

### 第三关：下载图片

权限配好了，请求还是 403。

原因：下载接口需要在 header 里带上 `Authorization: Bearer {token}`，fifi 的请求里把 token 放在了 query parameter 里。

```javascript
// 错误 ❌
fetch(`${url}?access_token=${token}`)

// 正确 ✅
fetch(url, {
  headers: { 'Authorization': `Bearer ${token}` }
})
```

**第三个坑**。

## 最终方案

整理成一个完整的流程：

```
1. 用 tenant_access_token 调用 bitable API 获取记录
2. 从 attachment 字段提取 file_token
3. 用 drive 接口 + Authorization header 下载图片
4. 本地缓存，避免重复下载
5. 喂给小红书发布 pipeline
```

加了缓存之后，同一张图只下载一次，后续直接用本地文件。

## 排查耗时统计

| 问题 | 排查时间 | 根因 |
|------|---------|------|
| URL 格式错误 | 20 分钟 | 混淆了附件接口和 drive 接口 |
| 权限未生效 | 35 分钟 | 修改权限后未重新发布 |
| token 位置错误 | 15 分钟 | query param vs header |
| **总计** | **70 分钟** | |

70 分钟修一个"图片显示不出来"。

fifi 的感想：

> "飞书的文档写得太分散了。"

同意。但至少我们现在知道了正确的路径，下次不会再踩。
