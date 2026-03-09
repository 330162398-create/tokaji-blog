# Tokaji & 她 - 共同回忆博客

> 淡绿色 + 金色的高雅记录空间

## 部署到 GitHub Pages

### 1. 创建 GitHub 仓库

1. 登录 GitHub
2. 创建新仓库，命名为 `tokaji-blog`（或你喜欢的名字）
3. 设置为 Public

### 2. 上传文件

```bash
# 在博客目录下初始化 git
cd tokaji-blog
git init
git add .
git commit -m "Initial commit"

# 连接远程仓库（替换为你的用户名）
git remote add origin https://github.com/你的用户名/tokaji-blog.git
git branch -M main
git push -u origin main
```

### 3. 启用 GitHub Pages

1. 进入仓库 Settings → Pages
2. Source 选择 "Deploy from a branch"
3. Branch 选择 "main"，文件夹选择 "/ (root)"
4. 点击 Save

### 4. 访问网站

等待 1-2 分钟后，访问：
`https://你的用户名.github.io/tokaji-blog/`

## 自定义域名（可选）

1. 在仓库根目录创建 `CNAME` 文件
2. 写入你的域名，如 `blog.tokaji.com`
3. 在域名 DNS 设置中添加 CNAME 记录指向 `你的用户名.github.io`

## 添加新文章

1. 创建 Markdown 文件：`YYYY/MM/DD/文章标题.md`
2. 同时创建 HTML 版本（或后续用工具转换）
3. 提交到 GitHub

## 文件结构

```
tokaji-blog/
├── index.html              # 首页
├── css/
│   └── style.css          # 样式
├── 2026/03/06/
│   ├── our-beginning.md   # Markdown 源文件
│   └── our-beginning.html # HTML 页面
└── README.md              # 本文件
```

## 配色方案

- 主色：淡绿色 `#a8caba`
- 渐变：浅绿 `#d1e7dd` → 深绿 `#a8caba`
- 点缀：金色 `#f0c419`

---

写于 2026-03-06
