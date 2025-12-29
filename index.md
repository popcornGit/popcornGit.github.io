---
title: "欢迎来到我的博客"
date: 2025-12-29
---

# 你好，世界

这是我用 GitHub Pages 写的第一篇博客。你可以直接在这里写 Markdown，GitHub 会自动渲染。
``` ````

可选：如果想启用 Jekyll 主题或做简单配置，添加一个 _config.yml（可选）：

```name=_config.yml
title: "我的 GitHub Pages 站点"
theme: minima
```

可选：如果使用自定义域，在仓库根加一个 CNAME 文件，内容为你的域名（例如 example.com）：

```name=CNAME
www.example.com
```

三、通过网页操作直接新建文件（不用本地 git）
1. 进入仓库首页，点击 “Add file” -> “Create new file”  
2. 在文件名处输入 `index.md`，把上面内容粘贴进去，Commit changes（默认在 main 分支）  
3. 等几分钟访问 https://popcornGit.github.io

四、通过命令行（推荐给经常本地开发的人）
在本地做：
- git init
- git add index.md _config.yml (如果有)
- git commit -m "初始 Pages"
- git branch -M main
- git remote add origin git@github.com:popcornGit/popcornGit.github.io.git
- git push -u origin main

五、在 Settings 中检查 / 启用 Pages（通常不必手动）
- 进入仓库 Settings -> Pages（或在新版 Settings -> Pages）  
- 对于个人仓库 popcornGit.github.io，默认会部署 root（main branch / root）并自动启用。若用项目站点，请选择 branch（例如 main）和 folder（/root 或 /docs）。  
- 如果你使用自定义域，填入域名并启用 Enforce HTTPS（GitHub 会尝试为自定义域申请证书，需正确设置 DNS）。

六、Jekyll、主题与文章（可选进阶）
- GitHub Pages 原生支持 Jekyll。如果你想写博客文章并使用 Jekyll 布局，可：
  - 在仓库根创建 _posts 目录，文件命名格式：YYYY-MM-DD-title.md，文件开头要有 front-matter（YAML）。
  - 使用主题：在 _config.yml 指定 theme（如 minima），或使用 GitHub Pages 主题选择器（Settings -> Pages -> Theme Chooser）。
- 如果需要更复杂的静态站（Hugo/Hexo/Next.js），建议本地构建后把静态文件推到 gh-pages 分支或使用 GitHub Actions 自动构建部署（这是进阶，不在“直接用 Pages”的最简流程里）。

七、常见问题与小提示
- 刷新/生效时间：提交后一般几分钟生效，有时 <10 min。
- 如果访问 404：确认仓库名是否为 username.github.io（个人站点）或 Pages 设置选择了正确分支/目录。
- 禁用 Jekyll（如果你不想让 GitHub 尝试使用 Jekyll 构建）：在仓库根放空文件 .nojekyll。
- 评论可以用 Giscus（基于 GitHub Issues）、Utterances 等，搜索并按说明配置。
- 想要我帮你把这些文件直接生成好（index.md、_config.yml、CNAME）或者生成一个基本的 _posts 示例，我可以把文件内容发给你，或指导你用命令行一步推上去。你想现在我帮你生成哪些文件？
