---
layout: wiki
title: Laravel
categories: [PHP]
description: Laravel 的一些用用法
keywords: Laravel, PHP
---

<span class="ec ec-deer"></span><span class="ec ec-deer"></span><span class="ec ec-deer"></span> Laravel 编码的最佳实践

## 安装 Laravel
在开发阶段，运行 Laravel 只需要 PHP + MySQL即可， PHP 7.* 内置了 HTTP 服务器配合 Laravel 的 artisan 脚本就可以让 Laravel 应用跑起来。 在 Windows 下推荐使用 Xampp 集成开发环境， Mac/Linux 下可以自己编译 PHP, MySQL.

通过 composer 安装指定版本的 Laravel 框架。 
```shell
composer create-porject laravel/laravel --prefer-dist "5.7.*"
```
composer 是 PHP 的包管理工具，默认源为国外源，建议在执行上面命令之前用以下命令切换到中国源。
```shell
composer config -g repo.packagist composer https://packagist.phpcomposer.com
```

最后，在框架根目录执行 ```yarn ``` 下载好前端资源，整个框架就下载完了。<span class="ec ec-clap"></span><span class="ec ec-clap"></span>

## Laravel 路由


