Тестовый проект. Инструкция по установке
========================================

Установка Composer
------------------
Если Composer еще не установлен это можно сделать по инструкции на getcomposer.org, или одним из нижеперечисленных 
способов. На Linux или Mac используйте следующую команду:

curl -sS https://getcomposer.org/installer | php
mv composer.phar /usr/local/bin/composer

На Windows, скачайте и запустите Composer-Setup.exe(https://getcomposer.org/Composer-Setup.exe).

В данном руководстве предполагается, что Composer установлен глобально. То есть он доступен через команду composer. 
Если вы используете composer.phar из локальной директории, изменяйте команды соответственно.

Если у вас уже установлен Composer, обновите его при помощи composer self-update.

Клонирование репозитория
------------------------
Находясь в целевой папке(например /home/www/test), выполните команду 
git clone https://github.com/DmitryKorobko/Test-task-yii2-basic.git

Настройка веб сервера
---------------------
Приложение, установленное по инструкциям, приведённым выше, будет работать сразу как с Apache, так и с Nginx под 
Windows и Linux с установленным PHP 5.4 и выше. Yii 2.0 также совместим с HHVM. Тем не менее, в некоторых случаях 
поведение при работе с HHVM отличается от обычного PHP. Будьте внимательны.

На рабочем сервере вам наверняка захочется изменить URL приложения с http://www.example.com/basic/web/index.php 
на http://www.example.com/index.php. Для этого необходимо изменить корневую директорию в настройках веб сервера так, 
чтобы та указывала на basic/web. Дополнительно можно спрятать index.php следуя описанию в разделе «Разбор и 
генерация URL». Далее будет показано как настроить Apache и Nginx.

Устанавливая basic/web корневой директорией веб сервера вы защищаете от нежелательного доступа код и данные, 
находящиеся на одном уровне с basic/web. Это делает приложение более защищенным.
 
Рекомендуемые настройки Apache
------------------------------
Добавьте следующее в httpd.conf Apache или в конфигурационный файл виртуального хоста. Не забудьте 
заменить path/to/basic/web на корректный путь к basic/web.

<div> #Устанавливаем корневой директорией "basic/web"
DocumentRoot "path/to/basic/web"

<Directory "path/to/basic/web">
    RewriteEngine on

    # Если запрашиваемая в URL директория или файл существуют обращаемся к ним напрямую
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteCond %{REQUEST_FILENAME} !-d
    # Если нет - перенаправляем запрос на index.php
    RewriteRule . index.php

    # ...прочие настройки...
</Directory>
</div>

Рекомендуемые настройки Nginx
-----------------------------
PHP должен быть установлен как FPM SAPI для Nginx. Используйте следующие параметры Nginx и не забудьте заменить 
path/to/basic/web на корректный путь к basic/web и mysite.local на ваше имя хоста.
<div>
server {
    charset utf-8;
    client_max_body_size 128M;

    listen 80; ## listen for ipv4
    #listen [::]:80 default_server ipv6only=on; ## слушаем ipv6

    server_name mysite.local;
    root        /path/to/basic/web;
    index       index.php;

    access_log  /path/to/project/log/access.log;
    error_log   /path/to/project/log/error.log;

    location / {
        # Перенаправляем все запросы к несуществующим директориям и файлам на index.php
        try_files $uri $uri/ /index.php?$args;
    }

    # раскомментируйте строки ниже во избежание обработки Yii обращений к несуществующим статическим файлам
    #location ~ \.(js|css|png|jpg|gif|swf|ico|pdf|mov|fla|zip|rar)$ {
    #    try_files $uri =404;
    #}
    #error_page 404 /404.html;

    location ~ \.php$ {
        include fastcgi.conf;
        fastcgi_pass   127.0.0.1:9000;
        #fastcgi_pass unix:/var/run/php5-fpm.sock;
    }

    location ~ /\.(ht|svn|git) {
        deny all;
    }
}
</div>

Используя данную конфигурацию установите cgi.fix_pathinfo=0 в php.ini чтобы предотвратить лишние системные вызовы stat().

Учтите, что используя HTTPS необходимо задавать fastcgi_param HTTPS on; чтобы Yii мог корректно определять защищенное соединение.

Структура проекта
-----------------

      assets/             contains assets definition
      commands/           contains console commands (controllers)
      config/             contains application configurations
      controllers/        contains Web controller classes
      mail/               contains view files for e-mails
      models/             contains model classes
      runtime/            contains files generated during runtime
      tests/              contains various tests for the basic application
      vendor/             contains dependent 3rd-party packages
      views/              contains view files for the Web application
      web/                contains the entry script and Web resources

Установка завершена, миграции не требуются =)
--------------------------------------------