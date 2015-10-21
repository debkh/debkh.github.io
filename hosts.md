# Настройка виртуальных хостов Apache в ![ubuntu](http://assets.ubuntu.com/sites/ubuntu/1533/u/img/logos/logo-ubuntu-orange.svg)

## Зачем это надо?

**Виртуальные хосты** значительно упрощают локальную разработку проектов и позволяют работать с несколькими проектами на разных доменных именах.
Изначально после установки веб-сервера Apache нам доступен один хост это [http://localhost/](http://localhost/)
Для создания других виртуальных хостов нужно проделать ряд несложных операций в комадной строке

### Шаг 1 

#### Структура директорий

По умолчанию Домашняя директория веб сервера **Apache 2** находится по пути

		/var/www
		
Я предлагаю хранить сайты в директории пользователя в папке ```projects```
Для начала создадим данную директорию командой:

		mkdir /home/<имя пользователя>/projects
либо:

		mkdir ~/projects
		
внутри этой папки я предлагаю такую организацию хранения файлов

		- home
		- - <Имя Пользователя>
		- - - projects
		- - - - <имя виртуального домена 1 например mysyte.dev>
		- - - - <имя виртуального домена 2>
		- - - - ...
		
 **Обратите внимание :**
 
** 1. linux позволяет создание директорий содержащих в своём имени точку **

** 2. для работы на локальной машине я рекомендую использовать доменные зоны .dev (сокращение от development) или .loc (сокращение от local) **

В корне данных директорий мы будем размещать файлы и папки наших проектов

### Шаг 2

#### Создание виртуальных хостов Apache

при стандартной установке apache2 конфиги с виртуальными хостами лежат в папке
		
		/etc/apache/sites-avalible/
там находится файл
 
		defaul.conf *
** * В некоторых случаях имя может быть 000-default.conf но это чаще всего свойственно Rad Hat Enterprise Linux, CentOS и Ubuntu версий выше чем 12.04 **
для начала создадим копию данного файла с именем ```<имя домена>.conf```

	sudo cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/mysyte.dev.conf
	
Откроем данный файл в текстовом редакторе чтобы изучить его содержимое:

	sudo gedit  /etc/apache2/sites-available/mysyte.dev.conf
в окне редактора gedit должно открться содержисое файла ```mysyte.dev.conf```

	<VirtualHost *:80>
	
	# The ServerName directive sets the request scheme, hostname and port that
	# the server uses to identify itself. This is used when creating
	# redirection URLs. In the context of virtual hosts, the ServerName
	# specifies what hostname must appear in the request's Host: header to
	# match this virtual host. For the default virtual host (this file) this
	# value is not decisive as it is used as a last resort host regardless.
	# However, you must set it for any further virtual host explicitly.
	
		#ServerName www.example.com

		ServerAdmin webmaster@localhost
		DocumentRoot /var/www/html

	# Available loglevels: trace8, ..., trace1, debug, info, notice, warn,
	# error, crit, alert, emerg.
	# It is also possible to configure the loglevel for particular
	# modules, e.g.
	
		#LogLevel info ssl:warn

		ErrorLog ${APACHE_LOG_DIR}/error.log
		CustomLog ${APACHE_LOG_DIR}/access.log combined

	# For most configuration files from conf-available/, which are
	# enabled or disabled at a global level, it is possible to
	# include a line for only one particular virtual host. For example the
	# following line enables the CGI configuration for this host only
	# after it has been globally disabled with "a2disconf".
	#Include conf-available/serve-cgi-bin.conf
	</VirtualHost>

Приведем файл к нужному нам виду и избавимся от лишних коментариев

	<VirtualHost *:80>
    	ServerAdmin webmaster@mysyte.dev
    	ServerName mysite.dev
    	ServerAlias www.mysite.dev
    	DocumentRoot /home/<Имя пользователя>/projects/mysite.dev
    	ErrorLog ${APACHE_LOG_DIR}/error.log
    	CustomLog ${APACHE_LOG_DIR}/access.log combined
	</VirtualHost>

Далее воспользуемся утилитой ```a2ensite``` (устанавливается вмесе с вебсервером)
для включения нашего сайта

	sudo a2ensite mysite.dev.conf
После данной операции надо перезапустить веб-сервер командой

	sudo service apache2 restart
	
### Шаг 3

#### Редактирование файла hosts
Почти во всех дистрибутивах Linux файл ```hosts``` располагается в:

	/etc/hosts
Для его редактирования нужно воспользоваться командой

	sudo gedit /etc/hosts
После этого вы увидите что-то похожее на

	127.0.0.1	localhost
	127.0.1.1	<имя вашего компьютераЮ
В конце файла добавляем строку

	127.0.0.1	mysite.dev
После этого создаем файл index.html в директории 

	~/projects/mysite.dev/
с содержимым 

	<html>
	<head>
	<title> IT works </title>
	</head>
	<body>
	<h1>IT Works</h1>
	</body>
	
и открываем в браузере домен
Если надпись IT Works корректно отобразилась - все настройки сделанны корректно и можно приступать к работе.