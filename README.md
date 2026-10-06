# douyin-detail — 给 AI 用的抖音链接全解（opencli 补充命令）

> **English TL;DR** — A complementary command for [opencli](https://github.com/jackwener/opencli):
> hand it any Douyin share link (short link / video URL / bare ID) and get structured output —
> caption, author, digg / comment / collect / share counts, publish time, duration, music,
> **no-watermark video URL**, cover, and photo-album images. It rides opencli's browser bridge:
> no cookie plumbing, no signature code — the request runs inside your logged-in browser session.
> Read-only, one link at a time.

一条命令，把一条抖音分享链接变成结构化数据：

> 文案 / 作者 / 点赞·评论·收藏·转发 / 发布时间 / 时长 / 背景音乐 /
> 无水印视频下载地址 / 封面 / 图文帖图集

为 [opencli](https://github.com/jackwener/opencli) 写的补充命令（本地适配器，不动官方包）。

## 为什么有它

opencli 官方的 douyin 适配器覆盖的是**创作者工作流**（自己账号的数据、发布、评论管理），
缺一条"消费向"的命令：给我一条任意链接，把这条视频说清楚。
本命令补的就是这一格——复用它现成的浏览器桥与登录态，不需要自己养 Cookie、不碰签名算法。

## 安装（两步）

1. 前提：装好 opencli 与浏览器扩展（见 opencli 官方 README），并在浏览器里登录 `douyin.com`
2. 把 `detail.js` 放进 `~/.opencli/clis/douyin/`（目录不存在就创建）

完成。命令列表里会多出 `douyin detail`。

## 用法

```bash
opencli douyin detail "https://v.douyin.com/xxxx/" -f yaml
opencli douyin detail "https://www.douyin.com/video/7xxxxxxxxxxxxxxxxxx" -f json
opencli douyin detail 7xxxxxxxxxxxxxxxxxx   # 纯 ID 也行
```

输出字段：`aweme_id` `desc` `author` `author_sec_uid` `digg` `comment` `collect` `share`
`created` `duration_s` `music` `play_url` `cover` `images` `share_url`

`author_sec_uid` 可以直接接着用官方的 `opencli douyin user-videos <sec_uid>` 看该账号全部作品。

## 原理

抖音的接口必须**在已登录页面上下文里**发起——网页自身的 JS 会给同源 `/aweme/` 请求
自动附加签名（a_bogus/msToken），从外面直接调会被风控拦。本命令复用 opencli 浏览器桥，
在页面里请求官方详情接口，签名的事交给抖音自己的页面代码。

## 边界与礼仪

- **只读单条链接**；不要用于批量采集；频率克制——账号安全第一
- 需要本机浏览器里存在已登录的 douyin 会话
- 抖音改版可能导致失效；本命令与官方适配器同命运
- 图文帖（`images` 字段）路径欢迎实测反馈

## License

MIT
