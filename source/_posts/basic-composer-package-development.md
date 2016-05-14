---
title: 动手开发自己的第一个 composer 包
date: 2016-05-13T15:28:41.000Z
tags:
  - PHP
  - composer
categories:
  - PHP
---

composer 是 PHP 的依赖管理工具，本篇文章就来说明如何构建一个包，并提交到 [Packagist](https://packagist.org/) ，这样别人就可以方便地通过 composer 使用你的包了。

开发 composer 包有以下几个步骤：

1. 初始化 composer.json 文件
2. 定义命名空间及包名
3. 实现包需要实现的功能
4. 提交到 GitHub
5. 在 Packagist 注册包

## 初始化 composer.json 文件

安装好 composer 后即可在本地运行 `composer init` 通过交互式命令行设置 composer.json 。

下面介绍其中的几个属性，以及常规的设置：

1. name<br>
  此属性定义包名，以 `/` 隔开，前面的为供应商名字，后面为包名，供应商代表 Packagist 网站为开发者提供的唯一的名字，用来组织包以及防止命名冲突。所以提交时最好先访问 <https://packagist.org/packages/yourvendorname> 将 url 中的 yourvendorname 替换为你想要取的名字，如果页面没有 404 ，说明已经被注册了。
2. license<br>
  许可证。关于许可证，建议看两篇文章，[开源项目 license 介绍](http://choosealicense.com/) 、[如何选择license](http://www.ruanyifeng.com/blog/2011/05/how_to_choose_free_software_licenses.html)
3. require<br>
  安装当前包所需的依赖。只有所有依赖被安装当前包才会被安装。
4. autoload<br>
  此配置下主要是 **PSR-4** 或者 **PSR-0** 设置，更推荐使用 PSR-4 标准。

<http://json-schema.org/> 上介绍了 JSON Schema 的定义以及各个语言对其各种功能的实现，有 validator 的实现，其中 [JSON Schema Validator](http://www.jsonschemavalidator.net/) 是在线的验证服务。其实最简单的就是使用 `composer validate composer.json` 来验证文件是否是有错误。

这是我演示的设置 composer.json 的视频

<script type="text/javascript" src="https://asciinema.org/a/45460.js" id="asciicast-45460" async="">
</script>

## 项目结构

以我开发的 [单点登录 SDK](https://packagist.org/packages/geosso/geosso) 为例，此项目基于 Laravel ，实现了站点接入单点登录系统的简单接入，应用只需在服务端注册并实现指定接口，即可接入 SSO 。

项目结构是典型的 MVC 结构，

```ascii
.
└── geo
    └── geosso
        ├── LICENSE
        ├── README.md
        ├── composer.json
        └── src
            ├── Contracts
            ├── Http
            │   ├── Controllers
            │   ├── Middleware
            │   └── Requests
            ├── ParamsBean
            ├── Providers
            ├── Support
            └── config

12 directories
```

LICENSE、README.md及composer.json 是运行 `tree -d` 之后手工添加上去的。

项目根目录定义在 src 下，在 composer.json 中也有定义，这样当 composer 加载这个包时就知道如何通过命名空间去解析文件路径。

Http 目录代表请求响应，之下的 Controllers 表示合法请求的控制器，Middleware 代表请求的第一道关卡，通过中间件去拦截请求，Requests 去获取前端请求并对请求过滤。

Contracts 代表接口定义。ParamsBean 代表应用层与底层服务沟通时的参数封装，通过 Bean 去获取各个参数，而不是传递 array 使得调用一致，并且强制接口调用时做类型检测，可以很大程度上统一各层之间的参数传递。

Providers 代表 Laravel 的服务容器，通过服务容器，可以注册路由与配置，加载助手类，绑定接口与其实现。

Support 就是一些助手类，对常用的与逻辑无关的功能的封装，config 代表应用自己的配置，通过 config 可以方便地将配置设置并使用全局函数 `config()` 调用。

## 提交至 GitHub

按照前面的步骤，一个包就有了基本的骨架，接下来就是上传至 GitHub ，配置项目，集成持续集成服务，发布开源项目许可证。

GitHub 初始化项目时可以选择生成 .gitignore 文件，选择许可证，初始化 README.md 文件，切换至本地的项目目录后，按如下步骤即可将目录上传至 GitHub：

```bash
>git init # 初始化仓库
>git remote set-url origin --push --add git@github.com:jayxhj/geosso.git #添加远程追踪仓库地址
> git add .
> git commit
> git push origin master
```

## 提交至 Packagist

Packagist 为 composer 默认获取包元数据信息的地址，从 Packagist 获取到元数据信息后，再从 GitHub 上拉取代码。因此，当把你开发的包上传至 GitHub 后还需要将其在 Packagist 注册，这样全世界的人都能通过 composer 去拉去你的代码了。

提交至 Packagist 只需三个步骤：

1. 注册帐号
2. 在 <https://packagist.org/packages/submit> 提交开发包
3. 设置 webhook 以便提交包更新后能及时地同步至 Packagist

自此，一个基本的包开发就结束了。通过 composer 来管理 PHP 的依赖，通过编写 composer package 去扩展自己的类库，通过引入其他的类库来填充自己的功能，就不用重复造轮子了。
