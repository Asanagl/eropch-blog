# eropch-blog

个人博客，基于 [Hexo](https://hexo.io/) + [reimu 主题](https://github.com/D-Sketon/hexo-theme-reimu)。

站点：https://eropchlight.top

## 本地开发

```bash
npm install        # 安装依赖
npx hexo server    # 本地预览 http://localhost:4000
npx hexo generate  # 生成静态站点到 public/
npx hexo clean     # 清理缓存 (db.json) 与 public/
```

## 发帖

```bash
npx hexo new post "my-post-slug"
```

- 文章放 `source/_posts/`，文件名用英文 slug
- frontmatter 包含 `title / date / tags / categories / description`
- 随机封面池：把图片（webp，≤1600w）放进 `source/_data/covers/` 即自动注册
- 头像：`source/_data/avatar/avatar.webp`

## 目录结构

```
source/
├── _posts/          # 文章
├── _data/            # 头像、随机封面池、友链数据
│   ├── covers/
│   ├── avatar/
│   └── backup_reimu/ # 换装前的原版素材备份
├── about/            # 关于页
├── friend/           # 友链
└── images/           # banner / favicon / 装饰元素
themes/reimu/         # 主题（vendored）
```
