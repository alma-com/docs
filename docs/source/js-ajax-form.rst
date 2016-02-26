Ajax форма
==========

Рассмотрим пример работы Ajax формы:
::
  <form method="POST" action="reg.html" class="js-ajax-form">
  <span class="js-alert"></span>
  <table>
    <tr>
      <td>Имя:</td>
      <td><input type="text" name="name" value=""></td>
      <td><span class="js-has-error" data-field="name"></span></td>
    </tr>
    <tr>
      <td>E-mail:</td>
      <td><input type="email" name="email" value=""></td>
      <td><span class="js-has-error" data-field="email"></span></td>
    </tr>
    <tr>
      <td>Пароль:</td>
      <td><input type="password" name="password" value=""></td>
      <td><span class="js-has-error" data-field="password"></span></td>
    </tr>
    <tr>
      <td>Подтверждение пароля:</td>
      <td><input type="password" name="password_confirmation" value=""></td>
      <td><span class="js-has-error" data-field="password_confirmation"></span></td>
    </tr>
    <tr>
      <td colspan="2"><input type="submit" name="submit" value="Регистрация"></td>
    </tr>
  </table>
  </form>

* ``js-ajax-form`` - Класс для инициализации Ajax формы.
* ``js-alert`` - Место куда будут выводиться сообщения.
* ``js-has-error[data-field="name_field"]`` - Место куда будут выводиться сообщения относящиеся к полю ``name_field``.
* ``has-error`` - класс добавляемый к полям, у которых есть сообщение.

Для того чтобы Ajax форма автоматически понимала, что ей делать, необходимо на обрабатывающей форму странице сформировать JSON следующего вида:
::
  {
    "status": "error", //Статус провреки success или error
    "message": {
      "field1" : "Поле field1 обязательно для заполнения.
    },
    "url" : "/my/url" //Редирект к URL. Является необязательным.
  }

Данный JSON быстро сформировать поможет класс `validator`_. Рассмотрим пример обрабатывающей форму страницы:
::
  $hash[
    $.email[required|email|unique:users]
    $.password[required|min:6|max:255]
    $.password_confirmation[same:password]
  ]
  $hash_result[^validator:make[$hash]]

  ^if($hash_result.status ne 'error'){
    ...
    $hash_result.url[^auth:get_url_login[]]
  }

  $result[^json:string[$hash_result]]



.. _`validator`: validator.html
