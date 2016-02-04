Установка
==========

Локальная установка ALMA.CMS v5
-------------------------
В данном примере рассматривается установка ALMA.CMS на локальный сервер XAMPP под ОС Window 7.
Если на сервере не установлен `Parser3`_, то для установки необходимо скачать `Программу установки для Win32`_ и установить его в папку ``C:\xampp\cgi-bin``.


Cклонировать ALMA.CMS v5 в папку ``mysite.local`` можно следующими командами:
::
	
	cd /C/xampp/htdocs/
	git clone https://bitbucket.org/alma-com/v5.git mysite.local
	cd mysite.local
	
На данный момент актуальная версия CMS хранится в ветке ``develop``, чтобы на нее переключиться и cделать её ``master``, необходимо выполнить следующии команды:
::

	git checkout develop
	git branch -D master
	git branch -m master
	
После этого необходимо импортировать БД в кодировке UTF-8. Файл для импорта БД называется ``v5.sql``

Далее необходимо скачать `NConvert для Windows`_ и файл ``nconvert.exe`` расположить ``C:/xampp/htdocs/mysite.local/cgi-bin/``.


Отлично, осталось немного. Находим в корне сайта файл ``config.example.p``, его необходимо скопировать и переименовать в ``config.p``. В этом файле настраивается подключение к БД и путь к NConvert. Вот что должно у вас получиться:
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
После того как сайт был локально развернут, необходимо создать репозиторий и слить в него все данные. Для примера рассматривается создание репозитория в `Bitbucket`_.
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
Первым делом необходимо сгенерировать открытый SSH-ключ для пользователя, в котором будет распологаться сайт. 

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
	
После этого в папке ``~/.ssh``, можно увидеть два файла id_rsa и id_rsa.pub (вместо id_rsa имя ключа, которое вы ввели). Скопируйте себе .pub файл, откройте в редакторе и всё его содержимое добавьте в настройки вашего сайта на Bitbucket. 

Найти этот разделы можно так: ``Страница проекта -> Settings -> Deployment keys``.

После этого необходимо склонировать с удаленного репозитория проект на боевой сервер:
::

	cd ~/путь_до_проекта/
	git init
	git remote add origin git@bitbucket.org:ваш_логин/mysite.ru.git
	git pull origin master
	
Далее необходимо настроить БД. Файл``config.p`` должен быть похож на это:
::

	$sql_table[mysite]
	$sql_login[root]
	$sql_pass[password]

В файле ``.htaccess``менять ничего не нужно.

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


.. _`Курс обучения Git на русском`: http://githowto.com/ru   
.. _`XAMPP`: https://www.apachefriends.org/ru/index.html
.. _`Parser3`: http://www.parser.ru/
.. _`Программу установки для Win32`: http://www.parser.ru/download/win32/
.. _`NConvert для Windows`: http://www.xnview.com/en/nconvert/
.. _`Bitbucket`: https://bitbucket.org
.. _`Create repository`: https://bitbucket.org/repo/create
.. _`parser.cgi`: http://www.parser.ru/download/
.. _`NConvert`: http://www.xnview.com/en/nconvert/