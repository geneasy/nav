# 网址导航（静态页面通用模板）

<https://geneasy.github.io/nav/>

这是一个通用的网址导航模板，任何类型的网站都可以拿去使用。

此网址导航页面是使用 [GenEasy](https://github.com/geneasy/geneasy) 文档生成工具 + [WebStack](https://github.com/WebStackPage/WebStackPage.github.io) 模板创建的静态页面，托管在 GitHub Pages 服务器上面。

当修改 `links.yml` 文件里的内容时，**GitHub Actions** 会自动更新 HTML 文件。不需要服务器，不需要数据库。

## 如何在我的网站里加入这个网址导航页面？

第一步：fork 这个项目 (注意: [gh-pages 分支一起复制](https://github.com/geneasy/links/issues/5))

第二步：进入 Settings > Pages, 启用 GitHub Pages 功能。分支选择 `gh-pages`。

第三步：2 分钟后，访问你的 GitHub Pages 域名，看看是否正常显示。`https://你的用户名.github.io/nav/` 或 `https://你的用户名.github.io/nav/index.html`

第四步：进入 Actions，启用 Actions 功能，进入 Settings，启用 Issues 功能。

第五步：

5.1 如果你的网站是托管在 GitHub Pages 上面，配置已经结束

5.2 如果你的网站部署在自己的服务器，需要添加一段 proxy 代码。

以下是 nginx 代码示例

```nginx
location /nav/ {
    proxy_pass      https://你的用户名.github.io/nav/;
    proxy_intercept_errors on;

    # allow GitHub to pass caching headers instead of using our own
    expires off;
    proxy_set_header   Host                   你的用户名.github.io;
    proxy_set_header   X-Host                 你的用户名.github.io;
}
```

5.3 如果你的网站部署在自己的服务器，不能（会）配置 proxy，你可以添加一个把生成的 HTML rsync 到你的服务器的 GitHub Action。

- [Burnett01/rsync-deployments](https://github.com/Burnett01/rsync-deployments)

```yml
name: Build and Deploy
on:
  push:
    branches:
      - gh-pages
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: rsync deployments
        uses: burnett01/rsync-deployments@5.1
        with:
          switches: -avzr --delete
          path: ./
          remote_path: /var/www/html/nav/
          remote_host: example.com
          remote_user: debian
          remote_key: ${{ secrets.DEPLOY_KEY }}
```

5.4 如果你的网站是托管在 Vercel, Netlify 等静态页面托管平台，需要添加部署到这些平台的 GitHub Action。(与 5.3 类似)

```yml
name: Build and Deploy
on:
  push:
    branches:
      - gh-pages
jobs: ...
```

第六步：访问你的网站，确认是否成功。`https://你的域名/nav/`

## Tips

### Tip: 如何修改 `links.yml`

#### 方法 1

把项目下载到本地，修改提交。

#### 方法 2

把现在 GitHub repository 的 URL 里的 `github.com` 改成 `github.dev`，会进入 web 版的 VS Code。在 web 版的 VS Code 里修改提交。

#### 方法 3

在 GitHub 点击 [`links.yml`](links.yml) 文件，然后点击编辑按钮。修改提交。

### Tip: 如何在本地修改数据，查看效果

1. clone repository 到本地

```sh
git clone https://github.com/geneasy/nav.git
```

2. 安装 `geneasy`

```sh
npm i -g geneasy
```

3. 执行下面命令生成 `index.html`

```sh
geneasy -t index.hbs -o public/index.html links.yml
```

`index.html` 生成在 `public` 目录下，确认生成的内容。

> [更多 `geneasy` 使用方法看官网](https://github.com/geneasy/geneasy)

4. 启动 web 服务器，确认效果。

可以使用 `http-server` 轻松完成。

```sh
npx http-server
```

或者安装后执行

```sh
npm i -g http-server
http-server
```

访问 `http://127.0.0.1:8080` (端口看终端日志输出内容) 可以确认效果。

> [更多 `http-server` 使用方法看官网](https://github.com/http-party/http-server)

### Tip: 可不可以不使用 GitHub 管理数据，不使用 GitHub Action 生成 HTML 和发布？

可以。

首先，把这个 repository clone 到你的电脑或服务器里，修改 links.yml 里的数据。

然后参考上面本地修改数据，查看效果的方法，生成 HTML，放到服务器指定文件夹里。

```sh
git clone https://github.com/geneasy/nav.git
npm i -g geneasy
geneasy -t index.hbs -o /path/to/nav/index.html links.yml
```

### Tip: 网址导航的 URL 可以不用 `nav`，用别的吗？

可以，

如果是网站部署在 GitHub Pages, 修改 GitHub repository 的名字。

如果是自己的服务器，修改 proxy 配置的 `location`。

```nginx
location / {
    proxy_pass      https://你的用户名.github.io/nav/;
    proxy_intercept_errors on;

    # allow GitHub to pass caching headers instead of using our own
    expires off;
    proxy_set_header   Host                   你的用户名.github.io;
    proxy_set_header   X-Host                 你的用户名.github.io;
}
```

or

```nginx
location /daohang/ {
    proxy_pass      https://你的用户名.github.io/nav/;
    proxy_intercept_errors on;

    # allow GitHub to pass caching headers instead of using our own
    expires off;
    proxy_set_header   Host                   你的用户名.github.io;
    proxy_set_header   X-Host                 你的用户名.github.io;
}
```

### Tip: 有其他模板或样式吗？

后续会添加，也非常欢迎你来贡献。

## 需要帮助？

如果遇到问题，需要帮助，请添加 [issue](https://github.com/geneasy/nav/issues)。

## Related

- [geneasy](https://github.com/geneasy/geneasy) - A command line tool that can easily generate HTML, Markdown documents, etc.
- [geneasy-links](https://github.com/geneasy/links) - 友情链接模板 - 这是一个通用的友情链接模板，任何类型的网站都可以拿去使用。
- [webstack](https://github.com/WebStackPage/WebStackPage.github.io) - ❤️ 静态响应式网址导航网站 - webstack.cc

## License

Copyright (c) 2021 [Pipecraft][my-url]. Licensed under the [MIT license][license-url].

## >\_

[![Pipecraft](https://img.shields.io/badge/site-pipecraft-brightgreen)](https://www.pipecraft.net)
[![DTO](https://img.shields.io/badge/site-DTO-brightgreen)](https://dto.pipecraft.net)
[![BestXTools](https://img.shields.io/badge/site-bestxtools-brightgreen)](https://www.bestxtools.com)

[my-url]: https://www.pipecraft.net
[license-url]: LICENSE
