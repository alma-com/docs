Установка
==========
Перед установкой ALMA.CMS необходимо установить систему контроля версий Git и настроить локальный сервер. 


Система контроля версий Git
-------------------------
Рекомендуется пройти `Курс обучения Git на русском`_. В данном курсе рассматривается установка, настройка и работа с Git.

Настройка локального сервера XAMPP
-------------------------
Для примера будем рассматривать настройку `XAMPP`_ на Windows 7.
Сама установка простая и настройки менять не нужно.

.. warning:: Если XAMPP не запускается, то скорее всего Skype занял 80-ый порт. (Skype -> Заходим в настройки Скайпа -> Дополнительно -> Соединение -> снимаем галочку `Использовать порты 80 и 443 в качестве альтернативных` -> Перегружаем Skype)

Для того чтобы создать локальный домен, необходимо:

1. В ``C:\xampp\htdocs`` создать папку с именем, с которым вы хотите создать домен, например ``mysite.local``.
2. В ``C:\xampp\apache\conf\extra\httpd-vhosts.conf`` необходимо написать следующее:
 ::

     <VirtualHost *:80>
         ServerAdmin webmaster@mysite.local
         DocumentRoot "C:/xampp/htdocs/mysite.local"
         ServerName mysite.local
         ServerAlias www.mysite.local
         ErrorLog "logs/error.log"
         CustomLog "logs/access.log" combined
     </VirtualHost>


 Если вам нужно чтобы ``localhost`` работал, то допишете в файл следующее:
 ::
	
	 <VirtualHost *:80> 
	     ServerName localhost 
	     DocumentRoot "C:/xampp/htdocs/xampp" 
	 </VirtualHost> 
3. В ``C:\Windows\System32\drivers\etc\host`` необходимо написать следующее:
 ::
     
	 127.0.0.1     mysite.local


Локальная установка ALMA.CMS v5
-------------------------
После того как был установлен Git и настроен локальный домен, далее если не установлен на сервере `Parser3`_, то его нужно установить. Для этого необходимо скачать `Программу установки для Win32`_ и установить его в папку ``C:\xampp\cgi``.


Далее необходимо склонировать v5 в папку ``mysite.local``:
::
	
	cd /C/xampp/htdocs/
	git clone https://bitbucket.org/alma-com/v5.git mysite.local
	cd mysite.local
	
На данный момент актуальная версия CMS хранится в ветке ``develop``, чтобы на нее переключиться и cделать её ``master``, необходимо выполнить следующую команду:
::

	git checkout develop
	git branch -D master
	git branch -m master
	
После этого необходимо настроить БД. Для этого переходим по URL ``http://localhost/phpmyadmin``, создаем базу данных ``mysite`` в кодировке UTF-8 и импортируем файл ``C:/xampp/htdocs/mysite.local/v5.sql``

Далее необходимо скачать `NConvert для Windows`_ и файл ``nconvert.exe`` расположить ``C:/xampp/htdocs/mysite.local/cgi-bin/``.


Отлично, осталось немного. Находим в корне нашего сайта файл ``config.example.p``, его необходимо скопировать и переименовать в ``config.p``. В этом файле настраивается подключение к БД и путь к NConvert. Вот что должно у вас получиться:
::

	$sql_table[mysite]
	$sql_login[root]
	$sql_pass[]

	$nconvert_path[/cgi-bin/]
	$nconvert_name[nconvert.exe]

Файл ``.htaccess.example`` тоже копируем и переименовываем в ``.htaccess``, открываем его и вместо второй строки вставляем следующую:
::

	Action parsed-html /cgi-bin/parser3.exe
	

Создание репозитория для сайта
-------------------------   
После того как сайт был локально развернут, необходимо для него создать репозиторий и слить в него все данные. Для примера рассматривается создание репозитория в `Bitbucket`_.
Находим кнопку `Create repository`_, имя репозитория вводим ``mysite.ru`` и сохраняем.

Далее в Git bash необходимо выполнить следующие команды:
::

	cd /C/xampp/htdocs/
	git remote rm origin
	git remote add origin https://bitbucket.org/ваш_логин/mysite.ru.git
	git push -u origin --all # pushes up the repo and its refs for the first time
	git push -u origin --tags # pushes up any tags
	
После этого вы имеете удаленный репозиторий для своего сайта!


Деплой на боевой сервер
-------------------------   
Для примера будет рассматриваться боевой сервер на ОС linux, с установленным Git, ISPmanager и с возможностью генерации открытого SSH-ключа.

Сперва необходимо в панеле управления ISPmanager создать пользователя ``mysite`` и создать домен ``mysite.ru``. 

После этого для данного пользователя необходимо сгененрировать открытый SSH-ключ. 

.. warning:: Если у пользователя уже имеется сгенерированный открытый SSH-ключ, то его создавать не нужно.

Для этого подключаемся по SSH к серверу. Если вы зашли под root-ом, то чтобы выполнять команды от имени ``mysite``, нужно прописать в консоль:
::

	sudo -u mysite bash
	
Далее выполняем команды, (если .ssh не существует - нужно создать: ``mkdir ~/.ssh``), при выполнении укажите любое имя для ключа, а поле пароля оставьте пустым.:
::

	cd ~/.ssh
	ssh-keygen -t rsa
	
Затем запускаем ssh агент и добавляем созданный ключ:
::

	ssh-agent /bin/bash
	ssh-add ~/.ssh/id_rsa
	
После этого в ISPmanager, если зайти под пользователем ``mysite``, в папке ``~/.ssh``, можно увидеть два файла id_rsa и id_rsa.pub (вместо id_rsa имя ключа, которое вы ввели). Скопируйте себе .pub файл, откройте в редакторе и всё его содержимое добавьте в настройки вашего сайта на Bitbucket. 

Найти этот разделы можно так: ``Страница проекта -> Settings -> Deployment keys``.

После этого необходимо склонировать с удаленного репозитория проект в наш сайт:
::

	cd ~/путь_до_проекта/
	git init
	git remote add origin git@bitbucket.org:ваш_логин/mysite.ru.git
	git pull origin master
	
После этого необходимо настроить БД. Для этого создаем базу данных ``mysite`` в кодировке UTF-8 и импортируем файл ``v5.sql``.

Далее находим в корне сайта файл ``config.example.p``, его необходимо скопировать и переименовать в ``config.p``. Открываем его и прописываем доступ к БД и убираем пути к NConvert. Вот что должно у вас получиться:
::

	$sql_table[mysite]
	$sql_login[root]
	$sql_pass[password]

Файл ``.htaccess.example`` тоже копируем и переименовываем в ``.htaccess``. Менять в нем ничео не нужно.

И последнее, в папке ``cgi-bin`` необходимо выставить права доступа 755 следующим файлам ``parser.cgi`` и ``NConvert``.

.. warning:: Если выдается ошибка Internal Server Error, то скорее всего побились бинарные файлы `parser.cgi`_ и `NConvert`_, необходимо скачать файлы под нужную ОС.


Автоматизация деплоя
-------------------------   
Для того чтобы не залезать каждый раз на сервер и не выполнять команду ``git pull``, необходимо:

1. В папке ``сgi-bin`` создать файл ``deploy.sh`` и вставить следующий код:
 ::
 
    #!/bin/bash
    echo Content-type: text/plain
    echo

    cd ..
    echo "----------------------------------------------"
    echo "|git commit on server:"
    echo "----------------------------------------------"
    git add -A
    git commit -m "---server commit---"

    echo
    echo "----------------------------------------------"
    echo "|git pull:"
    echo "----------------------------------------------"
    git pull -X theirs origin master

    echo
    echo "----------------------------------------------"
    echo "|git status:"
    echo "----------------------------------------------"
    git status

2. Файлу ``deploy.sh`` дать права доступа 755.
3. в Bitbacket-е ``Страница проекта -> Settings -> Webhooks`` необходимо добавить URL ``http://mysite.ru/deploy.sh``.

Теперь при каждом PUSH-е в удаленный репозиторий будет вызываться хук, который цепляет скрипт на сервере.

**Ты сделал это!** 

|good|

.. |good| image:: _static/good.jpg
   :width: 600 px


.. _`Курс обучения Git на русском`: http://githowto.com/ru   
.. _`XAMPP`: https://www.apachefriends.org/ru/index.html
.. _`Parser3`: http://www.parser.ru/
.. _`Программу установки для Win32`: http://www.parser.ru/download/win32/
.. _`NConvert для Windows`: http://www.xnview.com/en/nconvert/
.. _`Bitbucket`: https://bitbucket.org
.. _`Create repository`: https://bitbucket.org/repo/create
.. _`parser.cgi`: http://www.parser.ru/download/
.. _`NConvert`: http://www.xnview.com/en/nconvert/