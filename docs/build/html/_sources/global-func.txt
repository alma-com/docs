Глобальные функции
==========

.. function:: @text[]	
	
	Вывод основной части контента заполненного в административной панели.
	
.. function:: @h_menu[]	
	
	Вывод главного меню. Обычно логика формирования меню лежит ``$tmpl_path/p/h_menu.p``.
	
.. function:: @v_menu[]	
	
	Вывод дополнительного меню. Обычно логика формирования меню лежит ``$tmpl_path/p/v_menu.p``.
	
.. function:: @c_menu[]	
	
	Вывод меню на основе разделов каталога. Обычно логика формирования меню лежит ``$tmpl_path/p/с_menu.p``.
	
.. function:: @err404[]	
	
	Вывод содержимого 404 ошибки.
	
.. function:: @title[]	
	
	Вывод title страницы.
	
.. function:: @keywords[]	
	
	Вывод keywords страницы.
	
.. function:: @descript[]	
	
	Вывод description страницы.
	
.. function:: @descript[]	
	
	Вывод description страницы.
	
.. function:: @check_user[]

	Проверка что пользователь зарегестрирован. Если зарегистирован, то определяются следующие переменные:
	::
		$isRegUser[yes]
		$userid[$is.user_id] - id пользователя
	
.. function:: @loadparam[]
	
	Функция возвращает hash c  глобальными настройками сайта ``http://site.ru/manage/setting/`` в переменную ``$param``. Обычно функция вызывается перед отправкой почты.
	::
		$param.address - Адрес
		$param.inn - ИНН
		$param.kpp - КПП
		$param.bik - БИК
		$param.company	- Название компании
		$param.email - E-mail
		$param.postavchik - Поставщик
		$param.tel - Телефон
		$param.fax - Факс
		$param.rschet - Расчётный счёт
		$param.kschet - Корр. счёт
		$param.rukovod - Руководитель
		$param.buhgalter - Бухгалтер
		$param.bank - Банк


.. function:: @SendMailToAdmin[]
	
	Отправка почты `В Ваш Интернет-магазин поступил заказ` владельцу сайта.