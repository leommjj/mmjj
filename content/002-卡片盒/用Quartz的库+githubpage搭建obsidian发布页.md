---
{"publish":true,"created":"2025-09-19T11:48:30.000+08:00","modified":"2025-09-19T11:48:30.000+08:00","cssclasses":""}
---

# 准备工作
- 登录你的github账户；
- 获取github-token，获取方式可以问问ai更加详细，建议过期时间设置永久，免得重新替换；
- `Fork`这个项目或者`Use this template`到自己的库中：[quartz:](https://github.com/jackyzha0/quartz) 。如图：![[附件/Pasted image 20250916102644.png]]
- 设置`GitHub Actions`,步骤如下：![[附件/Pasted image 20250916103106.png]]
 在第三步，选择GitHub Actions后，输入下面的代码，或者去官网文档内[Hosting](https://quartz.jzhao.xyz/hosting)复制。
```
name: Deploy Quartz site to GitHub Pages
 
on:
  push:
    branches:
      - v4
 
permissions:
  contents: read
  pages: write
  id-token: write
 
concurrency:
  group: "pages"
  cancel-in-progress: false
 
jobs:
  build:
    runs-on: ubuntu-22.04
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0 # Fetch all history for git info
      - uses: actions/setup-node@v4
        with:
          node-version: 22
      - name: Install Dependencies
        run: npm ci
      - name: Build Quartz
        run: npx quartz build
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: public
 
  deploy:
    needs: build
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```
# 阶段性说明：至此github端设置完毕。
库中的`content`文件就是存储.md文件的位置，以后我们发布的文档都在这里，包括附件，而`quartz`文件夹则是网页的配置文件，你可以自定义网页名称标题等等信息。
![[附件/Pasted image 20250916104325.png]]

# 笔记软件端插件的设置
## 以obsidian的Quartz Syncer为例。
为啥是`Quartz Syncer`这个插件,事实上只要具备推送功能的插件，能把md文件上传到库中的`content`中都可以，甚至是手动上传。那么，这个插件具体需要设置地方不多，只需要配置三个地方即可。

![[附件/Pasted image 20250916105038.png]]
说明一下，Frontmatter这里，publish key的作用是，给文档添加一个名为“publish”的**复选框**属性后，就可以把该文档标记为发布文档。
![[附件/Pasted image 20250916105224.png]]
# 思源笔记端，可以用`发布工具`这个插件，发布平台选择Quartz。
坑点有三：
- 设置授权界面中的api地址就是库的浏览器地址，注意是根目录就行。
- 默认分支需要需库对应，例如现在拉取的版本是：V4。![[附件/Pasted image 20250916110903.png]]
- 存储路径和资源路径需要设置在`content`内，如下：![[附件/Pasted image 20250916111240.png]]
# 一点总结
我一开始折腾的时候，被网络上的教程一顿折磨，各种教程要把库克隆本地又是终端命令（命令提示符才有用）。最后，发现只需要拉取一个模板库，然后把文档推送上去就行了。有时间可以尝试一下其他的网站模板。踩坑的教程好几个，一个原因是我不懂编程语言，二是这些教程发布时间也有两三年了。比如ta：[如何使用 Quartz 和 Vercel 发布你的 Obsidian 笔记](https://www.catmuse.me/Thoughts/How-to-publish-Obsidian-notes-with-Quartz-on-Vercel)，还有一些就不记录了。收工。

反向链接：[[006-日记/2025-09-16\|2025-09-16]]



