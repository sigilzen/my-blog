---
title: "当前最流行的姿势用hugo--github--cloudflare搭建博客网站"
date: 2026-05-02T18:14:36+08:00
draft: false
language: en
featured_image: ./assets/cover.jpg
summary: 如何使用当前最流行的姿势搭建博客网站，hugo、github、cloudflare三剑客联合使用
author: xxj
authorimage: ../assets/images/global/author.webp
tags: ["编程"]
categories: ["Blog"]
---

今天在cloudflare上成功部署了hugo blog。算是开启了现代化的blog之路。

之前的typecho的文章慢慢迁移到这里吧。以后的blog发布，接入Hermes or openclaw，就更加的自动化了。

> 在macbookpro上尝试，安装hugo，并通过github部署到cloudflare上
![cover](./assets/cover.jpg)

## 一、 在macbookpro上安装hugo

```bash
# 首先，我们需要在你的电脑上安装 Hugo 和 Git。

#安装 Git： 去 git-scm.com 下载安装。安装后在终端输入 
git --version 确认成功。
❯ git --version
git version 2.50.1 (Apple Git-155)


#安装 Hugo： * Mac 用户： 直接执行 
brew install hugo
hugo version
hugo v0.155.0+extended+withdeploy darwin/arm64 BuildDate=2026-01-28T16:28:23Z VendorInfo=Homebrew

Windows 用户： 推荐用 choco install hugo-extended 或直接去 GitHub 下载 hugo_extended 版本的 .exe 文件。

验证： 输入 hugo version，看到版本号即成功。

#
# 1. 创建新站点
hugo new site my-blog
cd my-blog

# 2. 初始化 Git 仓库
git init

# 3. 添加一个主题（以流行的 PaperMod 为例）
git submodule add --depth=1 https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod

#写第一篇文章，内容自己随便写，markdown格式
hugo new posts/hello-world.md



# 4. 添加了另一个主题TailBliss，很符合blog+个人web展现的风格，方便扩展
# Add TailBliss as a theme (places it in themes/tailbliss/)
git submodule add https://github.com/nusserstudios/tailbliss.git themes/tailbliss

# 5. 在配置文件 config.toml 中指定主题
echo 'theme = "PaperMod"' >> hugo.toml
# Add TailBliss as a theme (places it in themes/tailbliss/)
git submodule add https://github.com/nusserstudios/tailbliss.git themes/tailbliss
```



## 二、设置github：my-blog

第三步：同步到 GitHub（云端“仓库”）

1. **注册 GitHub：** 登录 [github.com](https://github.com) 并创建一个新仓库（Repository），名字可以叫 `my-blog`。
2. **关联并上传：** 在你的本地终端继续输入（把 `YOUR_USERNAME` 换成你的）：

Bash

```bash
# 添加所有文件
git add .
git commit -m "first commit"

# 关联远程仓库并推送
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/my-blog.git
git push -u origin main
```

直接执行上面的操作，会报错，别担心，这是每个 GitHub 新手都会遇到的第一个“坑”。

**原因是：** GitHub 为了安全，已经在几年前禁用了“直接输入账号密码”的推送方式。现在的“Password”位置不能填你的登录密码，而是要填一个 **个人访问令牌（Personal Access Token，简称 PAT）**。

按照下面的步骤操作，只需一次就能解决：

------

> ##### 第一步：生成 GitHub 令牌 (Token)
>
> 1. 登录 GitHub，点击右上角头像 -> **Settings**。
> 2. 在左侧最下方找到 **Developer settings**。
> 3. 选择 **Personal access tokens** -> **Tokens (classic)**。
> 4. 点击 **Generate new token** -> **Generate new token (classic)**。
> 5. **Note** 随便填（比如 `my-hugo-blog`）。
> 6. **Expiration**（有效期）建议选 `No expiration`（或者选个长点的，省得过期又要重弄）。
> 7. **Select scopes**（权限）：勾选最上面的 **`repo`** 选项（这会选中它下面所有的子项）。
> 8. 点击页面最下方的 **Generate token**。
> 9. **关键：** 页面会显示一串以 `ghp_` 开头的字符。**立即复制并保存到你的笔记软件里**，因为它只会出现这一次，刷新就看不到了。
>
> ------
>
> ##### 第二步：更新你的 Git 推送地址
>
> 为了不用每次都输入那一长串 Token，我们直接把 Token 嵌入到你的项目地址里。
>
> 在你的终端（`my-blog` 目录下）输入以下命令：
>
> Bash
>
> ```bash
> # 先删除之前的远程地址
> git remote remove origin
> 
> # 重新添加包含 Token 的地址
> # 格式：git remote add origin https://你的Token@github.com/你的用户名/仓库名.git
> git remote add origin https://ghp_你的Token内容@github.com/sigilzen/my-blog.git
> ```
>
> **示例：** 如果你的 Token 是 `ghp_123456`，命令就是： `git remote add origin https://ghp_123456@github.com/sigilzen/my-blog.git`
>
> ------
>
> ##### 第三步：再次尝试推送
>
> 现在再执行推送命令，你会发现它直接成功，不再管你要密码了：
>
> Bash
>
> ```
> git push -u origin main
> ```
>
> ------
>
> ## 避坑指南
>
> - **Token 安全：** 刚才生成的 `ghp_` 开头的字符串就相当于你的“钥匙”，不要发给别人。
> - **以后还要输吗？** 只要你用了上面的“嵌入式”方法，这台电脑以后推送这个项目就不再需要验证了。
> - **Mac 用户小福利：** 如果你是 Mac 用户，其实也可以开启 macOS 的 **Keychain Access**（钥匙串），Git 会自动帮你存好，甚至不需要手动改 URL。
>
> 现在执行一下 `git push`，如果看到 `100% (done)` 这样的字样，就说明你的代码已经成功飞向 GitHub，Cloudflare 紧接着就会开始自动构建了！



## 三、部署到cloudflare

这一步最关键，也最省心。

1. **登录 Cloudflare 控制台：** 点击左侧菜单的 **Workers & Pages**。
2. **创建项目：** 点击 **Create application** -> **Pages** -> **Connect to Git**。
3. **授权：** 按照提示登录 GitHub，选中你刚才创建的 `my-blog` 仓库。
4. **配置构建设置：**
   - **Framework preset（框架预设）：** 选择 **Hugo**。
   - **Build command（构建命令）：** 保持默认 `hugo`。
   - **Build output directory（输出目录）：** 保持默认 `public`。
5. **环境变量（重点）：**
   - 点击 **Environment variables**，添加一条：
   - Variable name: `HUGO_VERSION`
   - Value: `0.128.0` (或者你本地 `hugo version` 查出来的版本)。
6. **保存并部署：** 点击 **Save and Deploy**。

---

搞定！你现在拥有了什么？

- **自动化：** 以后你每写一篇新文章，只需在本地运行 `git add .`、`git commit`、`git push`。
- **全球加速：** Cloudflare 会在几秒钟内自动感知到代码变化，重新编译并把新网页推送到全球上百个数据中心。
- **免费域名：** 你会得到一个类似 `my-blog.pages.dev` 的域名。当然，如果你在 Cloudflare 买了域名，可以一键绑定。

**进阶小建议：** 既然你平时习惯用 **P.A.R.A. 方法** 管理文件，你可以把你的 Hugo 项目文件夹放在你的 `Projects` 目录下。当你完成一篇 Markdown 笔记想公开发布时，直接把它拖进 `content/posts` 文件夹并执行 Git 提交即可。

成功！您的项目已部署到以下区域：全球

```bash
# 现在，把所有的改动推送到 GitHub：
git add .
git commit -m "feat: switch to tailbliss theme and add npm config"
git push
```





---

## 四、使用Tailbliss主题theme

太棒了！既然 PaperMod 已经跑通，说明你的 GitHub 到 Cloudflare 的链路已经彻底打通。

现在我们要从“手动档”切换到“自动档”。**Tailbliss** 相比 PaperMod，多了一个 **Tailwind CSS 编译** 的过程。我们不需要删除旧项目，直接在现有基础上原地升级。

按照以下步骤操作：

------

#### 第一步：本地清理与主题下载

我们需要先把 PaperMod 移除，换成 Tailbliss。在终端执行：

Bash

```bash
# 1. 删除旧主题
git rm themes/PaperMod
rm -rf themes/PaperMod

# 2. 添加 Tailbliss 主题
git submodule add https://github.com/tcarisland/tailbliss.git themes/tailbliss

# 3. 获取主题的示例配置（这一步最重要，它会帮你配好 Tailwind 环境）
# 建议手动拷贝主题自带的配置文件，覆盖掉你现在的 hugo.toml
cp themes/tailbliss/exampleSite/hugo.yaml ./
rm hugo.toml # 删掉旧的 toml，Tailbliss 习惯用 yaml
```



------

#### 第二步：同步 Node.js 配置文件

Tailbliss 依赖 `npm` 插件来生成漂亮的 CSS 样式。为了让 Cloudflare 知道该安装哪些插件，我们需要把主题目录下的 `package.json` 复制到根目录：

Bash

```bash
cp themes/tailbliss/package.json ./
```

------

#### 第三步：检查你的文章路径

Tailbliss 默认的文章目录可能和 PaperMod 略有不同。

1. 查看 `content` 文件夹。
2. 确保你的 `hello-world.md` 还在 `content/posts/` 目录下（或者根据 `hugo.yaml` 里的设置移动到对应的位置）。
3. **再次确认：** `draft: false` 依然是 false。

------

#### 第四步：修改 Cloudflare Pages 的构建指令

这是决定部署能否成功的核心。

1. 登录 **Cloudflare 控制台** -> **Workers & Pages** -> 你的项目。

2. 点击 **Settings** -> **Builds & deployments** -> **Edit configurations**。

3. **Build command（构建命令）** 修改为：

   Bash

   ```bash
   npm install && hugo
   ```

4. **Environment variables（环境变量）** 确保有两个：

   - `HUGO_VERSION`: `0.128.0` (或更高)
   - `NODE_VERSION`: `20` (或者 `18`)

------

#### 第五步：推送并观察

现在，把所有的改动推送到 GitHub：

Bash

```bash
git add .
git commit -m "feat: switch to tailbliss theme and add npm config"
git push
```

------

#### 💡 可能会遇到的一个小问题

如果你部署后发现页面样式“乱了”或者“字体没加载”，通常是因为 **Tailwind CSS 没有成功编译**。

你可以观察 Cloudflare 的 **Build Log (构建日志)**：

- 如果看到 `npm install` 报错，通常是 `package.json` 没放对位置。
- 如果看到 `hugo` 报错 `TOCSS: failed to transform...`，请检查环境变量里的 `HUGO_VERSION` 是否填对了，Tailbliss 必须要 **Extended** 版本的 Hugo。

---

### 🔍 执行后的预期结构检查

执行完这些后，你的 `my-blog` 文件夹应该是这样的：

* **`assets/`**: (新同步过来的，里面有 Tailwind 的源码)
* **`content/`**: (包含示例文章和你之前的文章)
* **`static/`**: (包含主题需要的图标和图片)
* **`hugo.yaml`**: (站点的核心配置文件)
* **`package.json`, `install.js`, `tailwind.config.js`**: (你手动补全的，专为 Cloudflare 准备的构建环境)

### 🚀 为什么这样做更靠谱？

使用 `rsync` 能把 `exampleSite` 中所有细碎的配置（比如多语言设置 `i18n` 或自定义数据 `data`）全部带过来。这样当你访问网页时，你会看到一个完整的、带有精美排版的官方 Demo 效果，而不是一个只有文字的简陋页面。

**操作完这一步后，Cloudflare 应该就能通过 `npm install && hugo` 完美生成带样式的站点了。** 试试看，这次是不是终于见到了 Tailbliss 那种现代感十足的页面？

![hugo-logo-wide](./assets/hugo-logo-wide.svg)