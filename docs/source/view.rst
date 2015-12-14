Представления
==========

Основы использования
-------------------------

Представления (views) содержат HTML-код, передаваемый вашим приложением. Это удобный способ разделения бизнес-логики и логики отображения информации. Представления находятся в каталоге ``class/block/nameYouBlock/views``.

Простое представление выглядит примерно так:

::

	<!-- Представление class/block/bnews/views/detail.pt -->

	<div class="bnews">
	    <h1>$name</h1>
	    <div class="date">$date</div>
	    <a class="photo fancybox_bgal" href="/images/bnews/b_${path}">
	        <img src="/images/bnews/m_$path" alt="">
	    </a>
	    }
	    ^untaint{$full_text}
	</div>
	

Подключение представления выглядет примерно так:
::
	$view[^registry:get[view]]
	^view.with[name;Наименование новости]
	^view.with[date;12 Сентября]
	^view.with[path;/bank/image.jpg]
	^view.with[full_text;Полный полный текст]
	^view.render[/class/block/bnews/views/detail.pt]
	

Передача данных в представление
-------------------------
::
	
	^view.with[name;Наименование новости]
	
В примере выше переменная ``$name`` со значением ``Наименование новости`` станет доступной для представления.


Передача данных во все представления
-------------------------

Иногда вам нужно передать данные во все представления вашего приложения. Для этого необходимо в ``auto.p`` в методе ``@main[]``, после инициализации представления ``$view[^view::create[]]`` дописать следующее:
::
	

	@main[]
	^use[/class/blocks.p]
	^use[/class/settings/settings.p]
	^use[/class/html.p]
	^use[/class/data/registry.p]
	^use[/class/data/view.p]

	^use[/class/user_func.p]

	^connect[$connect_string]{
	    ^get_param_global[]
	    ^get_param_local[]
	    $html[^html::init[$tmpl_info]]
	    $blocksinit[^blocks::init[$param_page]]
	    $breadcrumb[^breadcrumb[$param_page]]
		
	    $view[^view::create[]]
	    ^view.share[nameSite;Альма]	-	Пример подключение переменной во все шаблоны
		
	    ^registry:set[tmpl_path;$tmpl_path]
	    ^registry:set[view;$view]
		
	    ^all_content[]
	}
	