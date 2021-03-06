


### 数据库的迁移和安装方法
1.安装Sentry默认的数据库
php artisan migrate --package=cartalyst/sentry
2.创建自己的表
例如：
php artisan migrate:make create_articles_table --create=articles
这时候，去到 ./app/database/migrations，将会看到多出了两个文件，这就是数据库迁移文件，过一会我们将操作artisan将这两个文件描述的两张表变成数据库中真实的两张表，放心，一切都是自动的。

下面，在***_create_articles_table.php中修改：

Schema::create('articles', function(Blueprint $table)
{
	$table->increments('id');
	$table->string('title');
	$table->string('slug')->nullable();
	$table->text('body')->nullable();
	$table->string('image')->nullable();
	$table->integer('user_id');
	$table->timestamps();
});

php artisan migrate
这时候数据库中的articles表就建立完成了。

3. 尝试登录
用seed新增一名管理员，顺便新增一个管理员组。新建 app/database/seeds/SentrySeeder.php，内容为：

<?php

class SentrySeeder extends Seeder {

  public function run()
  {
    DB::table('users')->delete();
    DB::table('groups')->delete();
    DB::table('users_groups')->delete();
 
    Sentry::getUserProvider()->create(array(
      'email'      => 'oo@xx.com',
      'password'   => "ooxx",
      'first_name' => 'OO',
      'last_name'  => 'XX',
      'activated'  => 1,
    ));
 
    Sentry::getGroupProvider()->create(array(
      'name'        => 'Admin',
      'permissions' => ['admin' => 1],
    ));
 
    // 将用户加入用户组
    $adminUser  = Sentry::getUserProvider()->findByLogin('oo@xx.com');
    $adminGroup = Sentry::getGroupProvider()->findByName('Admin');
    $adminUser->addGroup($adminGroup);
  }
}


给 app/database/seeds/DatabaseSeeder.php 新增一行：

$this->call('SentrySeeder');
然后运行：

php artisan db:seed
成功以后，进数据库就会发现，users、groups、users_groups表均新增了一行。但是，articles表也新增了10行，对，seed就是这么蠢萌





## Laravel PHP Framework

[![Build Status](https://travis-ci.org/laravel/framework.svg)](https://travis-ci.org/laravel/framework)
[![Total Downloads](https://poser.pugx.org/laravel/framework/downloads.svg)](https://packagist.org/packages/laravel/framework)
[![Latest Stable Version](https://poser.pugx.org/laravel/framework/v/stable.svg)](https://packagist.org/packages/laravel/framework)
[![Latest Unstable Version](https://poser.pugx.org/laravel/framework/v/unstable.svg)](https://packagist.org/packages/laravel/framework)
[![License](https://poser.pugx.org/laravel/framework/license.svg)](https://packagist.org/packages/laravel/framework)

Laravel is a web application framework with expressive, elegant syntax. We believe development must be an enjoyable, creative experience to be truly fulfilling. Laravel attempts to take the pain out of development by easing common tasks used in the majority of web projects, such as authentication, routing, sessions, and caching.

Laravel aims to make the development process a pleasing one for the developer without sacrificing application functionality. Happy developers make the best code. To this end, we've attempted to combine the very best of what we have seen in other web frameworks, including frameworks implemented in other languages, such as Ruby on Rails, ASP.NET MVC, and Sinatra.

Laravel is accessible, yet powerful, providing powerful tools needed for large, robust applications. A superb inversion of control container, expressive migration system, and tightly integrated unit testing support give you the tools you need to build any application with which you are tasked.

## Official Documentation

Documentation for the entire framework can be found on the [Laravel website](http://laravel.com/docs).

### Contributing To Laravel

**All issues and pull requests should be filed on the [laravel/framework](http://github.com/laravel/framework) repository.**

### License

The Laravel framework is open-sourced software licensed under the [MIT license](http://opensource.org/licenses/MIT)
