数据库的迁移和安装方法

1.安装Sentry默认的数据库 php artisan migrate --package=cartalyst/sentry 2.创建自己的表 例如： php artisan migrate:make create_articles_table --create=articles 这时候，去到 ./app/database/migrations，将会看到多出了两个文件，这就是数据库迁移文件，过一会我们将操作artisan将这两个文件描述的两张表变成数据库中真实的两张表，放心，一切都是自动的。

下面，在***_create_articles_table.php中修改：

Schema::create('articles', function(Blueprint $table) { $table->increments('id'); $table->string('title'); $table->string('slug')->nullable(); $table->text('body')->nullable(); $table->string('image')->nullable(); $table->integer('user_id'); $table->timestamps(); });

php artisan migrate 这时候数据库中的articles表就建立完成了。

尝试登录 用seed新增一名管理员，顺便新增一个管理员组。新建 app/database/seeds/SentrySeeder.php，内容为：



<?php

class SentrySeeder extends Seeder {

public function run() { DB::table('users')->delete(); DB::table('groups')->delete(); DB::table('users_groups')->delete();

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


} }

给 app/database/seeds/DatabaseSeeder.php 新增一行：

$this->call('SentrySeeder'); 然后运行：

php artisan db:seed 成功以后，进数据库就会发现，users、groups、users_groups表均新增了一行。但是，articles表也新增了10行，对，seed就是这么蠢萌