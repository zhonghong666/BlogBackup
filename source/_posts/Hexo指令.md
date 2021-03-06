---
title: Hexo指令
date: 2020-10-16 12:02:10
author: zhonghong
summary: 主要介绍Hexo常用命令
keywords: Hexo
tags:
  - Hexo
---

# 全局安装Hexo

```shell
npm install -g hexo
```

# 初始化项目

```shell
hexo init [folder]
```

新建一个网站。如果没有设置 `folder` ，Hexo 默认在目前的文件夹建立网站。

该命令是一个快捷方式，运行以下步骤:

1. Git克隆hexo-starter，将hexo-theme-landscape包含到当前目录或指定的目标文件夹中。
2. 使用包管理器安装依赖项:纱线1、pnpm或npm，以已安装的为准;如果安装了多个，则列出优先级。npm默认与Node.js绑定。

# 创建新文章

```shell
# [layout] 为布局，可选项为 `post`、`page`、`draft`，这将决定文章所在文件路径。
# <title> 为文章标题
# 如 hexo new post 除了帅气，我还有啥！
hexo new [layout] <title>
```

该命令还可添加自选参数：

| 参数              | 描述                                          |
| ----------------- | --------------------------------------------- |
| `-p`, `--path`    | 自定义新文章的路径                            |
| `-r`, `--replace` | 如果存在同名文章，将其替换                    |
| `-s`, `--slug`    | 文章的 Slug，作为新文章的文件名和发布后的 URL |

默认情况下，Hexo 会使用文章的标题来决定文章文件的路径。对于独立页面来说，Hexo 会创建一个以标题为名字的目录，并在目录中放置一个 `index.md` 文件。你可以使用 `--path` 参数来覆盖上述行为、自行决定文件的目录：

```shell
hexo new page --path about/me "About me"
```

以上命令会创建一个 `source/about/me.md` 文件，同时 Front Matter 中的 title 为 `"About me"`

注意！title 是必须指定的！如果你这么做并不能达到你的目的：

```shell
hexo new page --path about/me
```

此时 Hexo 会创建 `source/_posts/about/me.md`，同时 `me.md` 的 Front Matter 中的 title 为 `"page"`。这是因为在上述命令中，hexo-cli 将 `page` 视为指定文章的标题、并采用默认的 `layout`。

# 生成静态文件

```shell
hexo generate
```

| **选项**              | **描述**                                                     |
| --------------------- | ------------------------------------------------------------ |
| `-d`, `--deploy`      | 文件生成后立即部署网站                                       |
| `-w`, `--watch`       | 监视文件变动                                                 |
| `-b`, `--bail`        | 生成过程中如果发生任何未处理的异常则抛出异常                 |
| `-f`, `--force`       | 强制重新生成文件<br/>Hexo 引入了差分机制，如果 `public` 目录存在，那么 `hexo g` 只会重新生成改动的文件。<br/>使用该参数的效果接近 `hexo clean && hexo generate` |
| `-c`, `--concurrency` | 最大同时生成文件的数量，默认无限制                           |

该命令可以简写为:

```shell
hexo g
```

# 发表草稿

```shell
hexo publish [layout] <filename>
```

# 启动服务器

```shell
hexo server
```

默认情况下，访问网址为： `http://localhost:4000/`。

| 选项             | 描述                           |
| ---------------- | ------------------------------ |
| `-p`, `--port`   | 重设端口                       |
| `-s`, `--static` | 只使用静态文件                 |
| `-l`, `--log`    | 启动日记记录，使用覆盖记录格式 |

# 部署网站

```shell
hexo deploy
```

| **参数**           | **描述**                 |
| ------------------ | ------------------------ |
| `-g`, `--generate` | 部署之前预先生成静态文件 |
|                    |                          |

该命令可以简写为：

```shell
hexo d
```

# 渲染文件

```shell
hexo render <file1> [file2] ...
```

| 参数             | 描述         |
| ---------------- | ------------ |
| `-o`, `--output` | 设置输出路径 |
|                  |              |

# 迁移内容

```shell
hexo migrate <type>
```

从其他博客系统 [迁移内容](https://hexo.io/zh-cn/docs/migration)。

# 清除

```shell
hexo clean
```

清除缓存文件 (`db.json`) 和已生成的静态文件 (`public`)。

在某些情况（尤其是更换主题后），如果发现您对站点的更改无论如何也不生效，您可能需要运行该命令。

# 列出网站资料

```shell
hexo hexo list <type>
```

# 查看版本

```shell
hexo version
```

# 选项

## 安全模式

```shell
hexo --safe
```

在安全模式下，不会载入插件和脚本。当您在安装新插件遭遇问题时，可以尝试以安全模式重新执行。

## 调试模式

```shell
hexo --debug
```

在终端中显示调试信息并记录到 `debug.log`。当您碰到问题时，可以尝试用调试模式重新执行一次，并 [提交调试信息到 GitHub](https://github.com/hexojs/hexo/issues/new)。

## 简洁模式

```shell
hexo --silent
```

隐藏终端信息。

## 自定义配置文件的路径

```shell
# 使用 custom.yml 代替默认的 _config.yml
hexo server --config custom.yml

# 使用 custom.yml 和 custom2.json，其中 custom2.json 优先级更高
hexo generate --config custom.yml,custom2.json,custom3.yml
```

自定义配置文件的路径，指定这个参数后将不再使用默认的 `_config.yml`。
你可以使用一个 YAML 或 JSON 文件的路径，也可以使用逗号分隔（无空格）的多个 YAML 或 JSON 文件的路径。例如：

```shell
# 使用 custom.yml 代替默认的 _config.yml
hexo server --config custom.yml

# 使用 custom.yml, custom2.json 和 custom3.yml，其中 custom3.yml 优先级最高，其次是 custom2.json
hexo generate --config custom.yml,custom2.json,custom3.yml
```

当你指定了多个配置文件以后，Hexo 会按顺序将这部分配置文件合并成一个 `_multiconfig.yml`。如果遇到重复的配置，排在后面的文件的配置会覆盖排在前面的文件的配置。这个原则适用于任意数量、任意深度的 YAML 和 JSON 文件。

## 显示草稿

```shell
hexo --draft
```

显示 `source/_drafts` 文件夹中的草稿文章。

## 自定义 CWD

```shell
hexo --cwd /path/to/cwd
```

自定义当前工作目录（Current working directory）的路径。