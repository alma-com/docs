Eloquent ORM
==========

Система объектно-реляционного отображения (ORM) Eloquent — красивая и простая реализация шаблона `ActiveRecord`_ для работы с базами данных.
Каждая таблица имеет соответствующий класс-модель, который используется для работы с этой таблицей.


Основы использования
-------------------------

Для начала создадим модель Eloquent. Модели располагаются в папке ``app\models``. Все модели Eloquent наследуют базовый класс ``eloquent`` и инициализируют переменную ``$_table`` с названием таблицы.

Пример создание модели:
::

	@CLASS
	role

	@BASE
	eloquent

	@auto[]
	$_table[roles]

Когда модель определена, у вас всё готово для того, чтобы можно было выбирать и создавать записи.



Методы Eloquent
-------------------------

.. function:: @all[]

	Получение всех записей модели.
	::

		$roles[^role:all[]]

.. function:: @find[string $id]

	Получение записи по любому уникальному полю.
	::

		$roles[^role:find[1]]

.. function:: @new[]

	Создание нового объекта модели.
	::

		$roles[^role:new[]]

.. function:: @empty[]

	Содержит пустоту, обычно используется при обновлении/добавлении записи.
	::

		$roles.name[^role:empty[]]

.. function:: @sql[]

	Выполнение произвольного sql запроса. ``%TABLE%`` - название таблицы, ``%KEY%`` - ключ таблицы.
	::

		$roles[^role:sql[SELECT %KEY% FROM %TABLE% LIMIT 2]]


.. function:: @save[]

	Сохранение данных в БД (только динамический вызов). Работает только для одной записи.
	::
		$roles[^role:find[admin]]
		$roles[^role.save[]]

.. function:: @delete[]

	Удаление данных из БД (только динамический вызов). Работает как для одной записи, так и для многих сразу.
	::
		$roles[^role:all[]]
		$roles[^role.delete[]]

.. function:: @get_table[]

	Получение название таблицы.
	::

		$name[^role:get_table[]]

.. function:: @get_key[]

	Получение название ключа таблицы.
	::

		$key[^role:get_key[]]

.. function:: @get_id[]

	Получение id записи, работает если только одна запись в моделе.
	::
		$roles[^role:find[admin]]
		$id[^role:get_id[]]

.. function:: @get_unique_fields[]

	Получение списка уникальных столбцов.
	::
		$fields[^role:get_unique_fields[]]



Пример сохранения новой записи
~~~~~~~~~~~~~
::

	$roles[^role:new[]]
	$roles.name[Администратор]
	$roles[^role.save[]]


Пример обновления записи
~~~~~~~~~~~~~
::

	$roles[^role:find[1]]
	$roles.name[Терминатор]
	$roles[^role.save[]]



Модель любой таблицы
-------------------------

Класс Eloquent позволяет обращаться к любой таблице без создания дополнительных классов.
::

	^eloquent:connect[roles]
	$roles[^eloquent:all[]]
	^eloquent:close[]

Метод ``connect`` устанавливает соединение с таблицей, с которой Eloquent должен работать. В конце необходимо вызвать метод ``clear``, для того чтобы удалить соединение с таблицей.


.. _`ActiveRecord`: https://ru.wikipedia.org/w/index.php?title=Special:Search&search=ActiveRecord
