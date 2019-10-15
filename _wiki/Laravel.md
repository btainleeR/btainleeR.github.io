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
通过路由别名之后, 别名和真实的路由地址成了 Key(别名)=>Value(路由地址)的映射关系。如果我们模板中多处用 ```route('home')``` 定义同一个路由，当我们想要修改路由地址的时候,只要修改在路由文件中的 Key 对应的 Value 值即可，而不用在模板中挨个修改 ```url('/home')``` 定义的路由。

### 路由分组
对一串路由进行控制。第一个参数用于定义分组规则。
```php
Route::group([], function(){
	Route::get('/home', function() {});
	Route::get('/admin', function() {});
});
```
#### 分组中间件
通过中间件审核为‘合法’请求放行。
```php
Route::group(['middleware'=>'auth'], function(){});
Route::middleware('auth')->group(function() {
	Route::get('/home', function() {});
	Route::get('/admin', function() {});
});
```
#### 分组前缀
大家都有/api前缀，就不要每次都写了。
```php
Route::group(['prefix'=>'api'], function(){});
Route::prefix('api')->group(function() {
	Route::get('/home');	//xxx.com/api/home
	Route::get('/admin');
});
```
#### 分组子域名
不一定要在Nginx层面区分不同域名哟~
```php
Route::domain('xxx.com')->group(function() {
	Route::get('/home', function() {});
});
```
如果子域名超级多的话~
```php
Route::domain('{name}.xxx.com').group(function() {
	Route::get('/home', function($name) {

	});

	Route::get('/user/{id}', function($name, $id){

	});
});
```
#### 分组子命名空间
如果大家委托的控制器都在同一个目录下的话~
```php
Route::namespace('Admin')->group(function() {

});
```
#### 分组路由命名前缀
如果大家命名前缀相同的话~
```php
Route::name('user.')->group(function() {
	Route::get('/home', function() {

	})->name('profile'); //route('user.profile')
});
```
### 路由高级玩法
#### 路由与模型绑定
通过ID查询一条数据？
```php
Route::get('task/{task}', function(\APP\Model\Task $task) {
	dd($task);
});
```
不想通过ID查，想通过Name字段查询只要在对应模型文件中修改getRouteKeyName()方法。
```php
class Task
{
	public function getRouteKeyName()
	{
		return 'name';
	}
}

```
#### 兜底路由
不要怕，不会再有404了
```php
Route::fallback(function() {

});
```

#### 频率限制
我怀疑你不是人~ 通过 ```throttle``` 中间件控制访问频率。
```php
Route::middleware('throttle.60,1')->group(function() {
	Route::get('/user', function() {

	});
});
```
更为细致的频率限制，为不通的路由限制不通的频率
```php
Route::middleware('throttle.limit_rate,1')->group(function() {
	Route::get('/user', function() {
		//在User模型中定义limit_rate的值
	});
});
```

## Laravel控制器
控制器是 MVC 模型中的 C(Controller), MVC 的思想就是一个 HTTP 请求进入框架交给 Controller 处理, Controller 到 M(Model) 中 CURD 数据，然后将数据渲染到 V(View) 中，返回给用户渲染后的 HTML页面。  
在Laravel中创建控制器：
```bash
php artisan make:controller HomeController
```
如果有个路由是 ``` Route::get('/home', 'HomeController@Index'); ```, 那么就要在 ```HomeController``` 中定义 index 方法。
```php
class HomeController
{
	public function index()
	{
		return 'xx';
	}
}
```
在控制器中获取 xxx.com/user/1 中的参数 1 以及渲染视图。
```php
class HomeController
{
	public function index(Request $request)
	{
		$user = $request->input('user');
		return view('xxx')->with(['user' = > $user]);
		//return view('xxx')->compact('user');
	}
}
```
快速生成 CURD/REST 控制器。
```shell
php artisan make:controller OrderControlelr --resource
```
会自动生成 CURD 的所有成员方法，配合特定的路由可以直接接管所有成员方法。
```php
Route::resource('order','OrderController');
```
然后通过 ``` php artisan route:list``` 命令即可查看路由命名，在模板中用 route() 方法生成对应 URL 即可。

## 表单请求方法及CSRF攻击防护

HTTP 请求最常见的方式就是 GET/POST 两种, HTML 表单也仅支持这两种方式。 但是 HTTP 协议还定义了很多其他的方式, 如 HEAD、 PUT、 TRACE、 CONNECT、 PATCH　和 OPTION。Javascript 的 XMLHttpRequest 对象进行 CROS 跨域资源共享时, 就是使用 OPTIONS 方法发送嗅探请求来判断服务器是否支持跨域访问。
#### 表单伪造
```php
Route::get();
Route::post();
Route::put();
Route::patch();
Route::delete();
Route::options();
```
Laravel 提供了表单伪造的方法让 HTML 支更多的请求方式。只要在 Form 表单中 添加名为 ```_method``` 的隐藏域即可。
```html
<form action="xxx" method="POST">
	<input type="hidden"  name="_method" value="DELETE">
</form>
```
#### CSRF 保护

在 Laravel 中可以直接访问 【 只读 】 路由(GET/HEAD/OPTIONS), 如果访问的是 【 写入】 路由(POST/PUT/PATCH/DELETE), 则需要一个隐藏的 ```_token``` 字段, 否则 Laravel 会返回一个 419 错误。  
如果是表单执行请求, 那么在表单中添加一个隐藏字段即可。
```html
<form action="xxx" method="POST">
	<input type="hidden" name="_token" value="{{ csrf_token() }}">
</form>
```
如果是在 Javascript 中执行HTTP请求,首先要在 ```<head>``` 中添加一个 ```<meta>``` 元素来存储 Token 值。
```html
<head>
	<meta name="csrf-token" content="{{ csrf_token() }}">
</head>
``` 
然后在请求之前将该值设置为请求头。
```javascript
//Jquery
$.ajaxSetup({
	headers: {
		'X-CSRF-TOKEN': $('meta[name="csrf-token"]').attr('content');
	}
});
// Vue.js
Vue.http.interceptors.push((request, next) {
	request.headers['X-CSRF-TOKEN'] = document.querySelector('#csrf-token').getAttribute('content');
	next();
});
// 如果使用了 asset/js/bootstrap.js, 则不用编写 Vue 请求头, 因为逻辑已经被其包含。 但是 meta 标签还是要写。
````

#### 排除指定 URL 可以跳过 CSRF 校验
如果使用了第三方支付系统的回调功能, 这个时候就需要从 Laravel 的 CSRF 中间件排除回调的路由, 因为第三方支付系统不知道传递什么 ```_token``` 值给自定义路由。  
有两种方式实现这个功能，第一种就是将路由添加到 ```routes/api.php```, 另外就是 在 VerifyCsrfToken 中间件中添加要排除的 URL。
```php
class VerifyCsrfToken extends middleware
{
	protected $except = [
		'alipay/*',
		'xxx.com'
	];
}
``` 

## Blade模板引擎


