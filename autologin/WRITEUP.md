# Автоматический вход: Write-up

Откроем [сайт](https://day6.upml.tech), ссылку на который нам дали в условии. 
Заметим, что поля username и password уже заполнены.

Можно попробовать нажать на кнопку "*Log in*", но после входа ничего интересного
не наблюдаем. Значит наш флаг, скорее всего, является паролем для входа на сайт.

Если зайти (на компьютере) с браузера *Google Chrome* или *Chromium*, то при
входе он предложит сохранить пароль. А рядом с самим паролем есть кнопка
"*показать пароль*". Если мы на неё нажмём, то увидим флаг.

Если такого вопроса от браузера не было, то можно посмотреть исходный код
страницы (предварительно нажав "*Log out*") и найти js-код, который
устанавливает значение пароля:

```html
<script>
    $('#password').val('so_insecure');
</script>
```

Или можно изменить тип поля для ввода пароля с `type="password"` на
`type="text"` и посмотреть этот пароль в самом поле.
```html
<input class="form-control " id="password" name="password" placeholder="Enter password" required="" type="text" value="">
```

Флаг: **so_insecure**

