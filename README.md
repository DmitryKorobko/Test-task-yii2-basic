Тестовый проект. Инструкция по установке
========================================

Установка Composer
------------------
Если Composer еще не установлен это можно сделать по инструкции на getcomposer.org, или приведённым ниже способом. 
На Linux или Mac используйте следующую команду:

    curl -sS https://getcomposer.org/installer | php
    mv composer.phar /usr/local/bin/composer

Если у вас уже установлен Composer, обновите его при помощи composer self-update.

Клонирование репозитория
------------------------
Находясь в целевой папке(например /home/www/test), выполните команду 
     
    git clone <repository link>

Настройка веб сервера
---------------------
В файле /etc/hosts, необходимо прописать хост, например:

    127.0.0.1	test.loc
 
Создать файл конфига в папке /etc/apache2/sites-available с расширением .conf, например test.loc.conf со следующим 
содержимым:


    <VirtualHost *:80>
        ServerName test.loc
        ServerAlias test.loc
        DocumentRoot /home/steinmann/www/test/web
        <Directory /home/steinmann/www/test/web>
            RewriteEngine on
            RewriteCond %{REQUEST_FILENAME} !-f
            RewriteCond %{REQUEST_FILENAME} !-d    
            RewriteRule . index.php
            AllowOverride All
        </Directory>
    </VirtualHost>


После чего необходим перезагрузить Apache командой:
sudo service Apache2 restart
 
Рекомендуемые настройки Apache
------------------------------
Добавьте следующее в httpd.conf Apache или в конфигурационный файл виртуального хоста. Не забудьте 
заменить path/to/basic/web на корректный путь к basic/web.

Устанавливаем корневой директорией "basic/web"

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