## 概述

QK Token System 的管理后台，包括会员管理、资产管理、交易记录、提现管理等功能。[Github](https://github.com/yyxo/qk-token-system-admin)

## 运行环境需求

该项目基于Laravel5.8开发，运行环境需满足如下要求：

* PHP >= 7.1.3
* OpenSSL PHP 扩展
* PDO PHP 扩展
* Mbstring PHP 扩展
* Tokenizer PHP 扩展
* XML PHP 扩展
* Ctype PHP 扩展
* JSON PHP 扩展
* BCMath PHP 拓展

[相关文档](https://learnku.com/docs/laravel/5.8/installation/3879#df1b28)

## 安装部署

1. 克隆项目文件到服务器
1. 将public目录设置为WEB站点根目录，public/index.php为入口文件
1. Linux系统需要设置storage目录和bootstrap/cache可写权限
1. 运行composer install命令安装插件
1. 执行迁移文件命令php artisan migrate，添加三个系统表到数据库
1. 导入database/admin.sql到数据库，会添加一条管理员数据，账号密码见SQL文件中的注释
1. 访问项目地址，输入管理员账号密码进行登录

[相关文档](https://learnku.com/docs/laravel/5.8/installation/3879#224e2c)

[迁移文档](https://learnku.com/docs/laravel/5.8/migrations/3928#running-migrations)
