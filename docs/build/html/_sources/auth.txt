Авторизация
==========

Класс авторизации находится ``app\auth.p``. Данный класс можно расширять собственными методами, а также переопределять встроенные методы.

По умолчанию логином является E-mail.


Методы класса
-------------------------

.. function:: @login[id_user]

  Ручной вход в систему по id или логину пользователя. ::

  ^auth:login[test@gmail.com]

.. function:: @logout[]

  Выход из системы текущего авторизованного пользователя. ::

  ^auth:logout[]

.. function:: @logout_all[id_user]

  Выход пользователя из системы со всех устройств. Где ``$id_user`` - id или логин пользователя. ::

  ^auth:logout_all[test@gmail.com]

.. function:: @attempt[login;password;remember]

  Попытка авторизации пользователя. Возвращает ``true`` или ``false``. Для того чтобы запомнить пользователя, необходимо передать ``$remember``.
  ::
    ^if(^auth:attempt[test@gmail.com;MyPass;1]){
      Вы авторизованны!
    }{
      Неудачная попытка
    }

.. function:: @user[]

  Получение текущего авторизованного пользователя. Возвращает модель `Eloquent`_.
  ::
    $user[^auth:user[]]
    $user.email - вывод e-mail пользователя


.. function:: @check[]

  Определение, авторизован ли текущий пользователь. Возвращает ``true`` или ``false``.
  ::
    ^if(^auth:check[]){
      Вы авторизованны!
    }{
      Пожалуйста авторизуйтесь!
    }

.. function:: @get_url_login[]

  Получение URL авторизации.

.. function:: @get_action_login[]

  Получение URL отправки формы при авторизации.

.. function:: @get_url_forgot[]

  Получение URL забыли пароль.

.. function:: @get_action_forgot[]

  Получение URL отправки формы забыли пароль.

.. function:: @get_url_registration[]

  Получение URL регистрации.

.. function:: @get_action_registration[]

  Получение URL отправки формы при регистрации пользователя.

.. function:: @get_url_personal[]

  Получение URL личного кабинета.

Вывод стандартных форм
-------------------------

.. function:: @show_form_login[]

  Форма авторизации.

.. function:: @login_request[]

  Выполнение запроса на авторизацию пользователя.

.. function:: @show_form_registration[]

    Форма регистрации.

.. function:: @registration_request[]

  Выполнение запроса на регистрацию пользователя.

.. function:: @show_form_forgot[]

    Форма восстановления пароля.

.. function:: @forgot_request[]

  Выполнение запроса на восстановление пароля.

.. function:: @show_form_forgot_password[]

    Форма изменения пароля при восстановлении пароля.

.. function:: @forgot_password_request[]

    Выполнение запроса на сброс пароля.


Пример создание своей формы авторизации
-------------------------

Для того чтобы переопределить стандартную форму авторизации, необходимо в ``app\auth.p``, создать метод ``@show_form_login[]`` со своей формой.
::
  @CLASS
  auth

  ...

  @show_form_login[]
  <form method="POST" action="^auth:get_action_login[]" class="js-ajax-form">
  <span class="js-alert"></span>
  <table>
    <tr>
      <td>E-mail:</td>
      <td><input type="email" name="email" value=""></td>
    </tr>
    <tr>
      <td>Пароль:</td>
      <td><input type="password" name="password" value=""></td>
    </tr>
    <tr>
      <td colspan="2">
        <label for="remember">
          <input type="checkbox" id="remember" name="remember" value="1">
          Запомнить меня на этом компьютере
        </label>
      </td>
    </tr>
    <tr>
      <td colspan="2">
        <input type="submit" name="submit" value="Войти">
        <a href="^auth:get_url_forgot[]">Забыли пароль?</a>
      </td>
    </tr>
  </table>
  </form>


.. _`Eloquent`: eloquent.html
