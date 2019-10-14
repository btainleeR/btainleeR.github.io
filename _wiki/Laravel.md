---
layout: wiki
title: Laravel
categories: [PHP]
description: Laravel 的一些用用法
keywords: Laravel, PHP
---

<span class="ec ec-deer"></span><span class="ec ec-deer"></span><span class="ec ec-deer"></span> Laravel 编码的最佳实践 参考自[学院君](https://xueyuanjun.com/laravel-tutorial-5_7)(良心免费教程)

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

Laravel中的路由可不仅仅是 URL => Function 的映射, 其包含了路由分组, 路由中间件, 访问控制等一系列路由控制功能。在 Laravel 中，路由文件的位置是 routes/web.php, 此文件主要用于处理来自浏览器的直接访问请求 。

### 基础路由
回应特定 URL请求。
```php
// 简单路由
Route::get('/', function() {
	return 'Hello, Laravel';
});
// 路由动作
Route::post('/', function() {});
Route::put('/', function() {});
Route::delete('/', function() {});
// 以上的路由功能只能写在 回调函数 function(){} 中，显然将所有功能写在一个文件中是不合理的。
// 委托路由： 只要访问 xxx.com/ 就会把请求递交给 IndexController 的 Index 方法处理。
Route::get('/', 'IndexController@index');
```

### 路由参数
获取 xxx.com/user/2 中的 2。

```php
// 获取用户 ID
Route::get('/user/{id}', function($id) {
// Use $id
});
// 用户ID 默认值
Route::get('/user/{id?}', function($id = 1) {
// 访问 xxx.com/user, 则 $id = 1;
});
// 正则参数过滤
Route::get('/user/{id}', function($id) {
// User $id
})->where('id', '[0-9]+');
```
### 路由命名
给路由起个别名。 
```php
Route::get('/home', function() {})->name('home');
```
如果没有命名路由, 在 Laravel Blade 中需要这样生成 URL　地址。
```php
url('/home') 
```
而通过命名之后, 就可以通过下面这种方式生成路由。
```php
route('home')
```
通过路由别名之后, 别名和真实的路由地址成了 Key(别名)=>Value(路由地址)的映射关系。如果我们模板中多处用 ```route()``` 定义同一个路由，当我们想要修改路由地址的时候,只要修改在路由文件中的 Key 对应的 Value 值即可，而不用在模板中挨个修改 ```url()``` 定义的路由。


