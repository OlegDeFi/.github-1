# 💵 Создание платежа

С Tegro.money и продавец, и покупатель получают «электронного кассира», который значительно упрощает проведение операций и ускоряет платежи.

Для создания платежа нужно передать необходимые параметры на специальный урл [https://tegro.money/pay/?params](https://tegro.money/pay/?params)

### Обязательные параметры

<table><thead><tr><th width="239">Ключ</th><th>Описание</th></tr></thead><tbody><tr><td>shop_id</td><td>Публичный ключ проекта</td></tr><tr><td>amount</td><td>Сумма платежа</td></tr><tr><td>order_id</td><td>Идентификатор заказа (номер платежа или email клиента)</td></tr><tr><td>currency</td><td>Валюта платежа (RUB, USD, EUR)</td></tr><tr><td>sign</td><td>Подпись запроса</td></tr></tbody></table>

### Дополнительные параметры

<table><thead><tr><th width="220">Ключ</th><th>Описание</th></tr></thead><tbody><tr><td>lang</td><td>Язык интерфейса (ru, en)</td></tr><tr><td>test</td><td>Если указан со значением "1" - оплата пройдет в тестовом режиме</td></tr><tr><td>payment_system</td><td>ID платежной системы</td></tr><tr><td>success_url</td><td>Урл успеха</td></tr><tr><td>fail_url</td><td>Урл ошибки</td></tr><tr><td>notify_url</td><td>Урл уведомлений</td></tr></tbody></table>

Для формирования подписи необходимо отсортировать по ключу все обязательные параметры, объединить пары ключ/значение символом **&** и добавить в конец Ваш секретный ключ. Затем захешировать получившуюся строку MD5, например:

```php
<?php 
$secret = 'GB%^&*YJni677';
$data = array(
    'shop_id'=>'D0F98E7D7742609DC508D86BB7500914',
    'amount'=>100,
    'currency'=>'RUB',
    'order_id'=>'123',
);
ksort($data);
$str = http_build_query($data);
$sign = md5($str . $secret);
```

**Внимание!** Если в форму оплаты был передан флаг тестовой оплаты test=1, этот параметр так же участвует в формировании подписи:

```php
<?php 
$secret = 'GB%^&*YJni677';
$data = array(
    'shop_id'=>'D0F98E7D7742609DC508D86BB7500914',
    'amount'=>100,
    'currency'=>'RUB',
    'order_id'=>'123',
    'test'=>1,
);
ksort($data);
$str = http_build_query($data);
$sign = md5($str . $secret);
```

Возможен переход сразу в платежную систему, если Вы готовы передать все данные для оплаты во входящем запросе. Для этого отправить данные нужно методом POST на урл https://tegro.money/pay/form/ обязательно указать параметр payment\_system и передать все обязательные поля для этого способа оплаты. В большинстве случаев это **email**, для дополнительной информации обратитесь в службу поддержки.

Пример:

```html
<form action="https://tegro.money/pay/form/" method="post">
<input type="hidden" name="shop_id" value="D0F98E7D7742609DC508D86BB7500914">
<input type="hidden" name="amount" value="100">
<input type="hidden" name="order_id" value="123">
<input type="hidden" name="lang" value="ru">
<input type="hidden" name="currency" value="RUB">
<input type="hidden" name="payment_system" value="11">
<input type="hidden" name="fields[email]" value="user@site.ru">
<input type="hidden" name="sign" value="e51845e62b106d245cc96c431d8aae42">
<input type="submit" value="Оплатить">
</form>
```
