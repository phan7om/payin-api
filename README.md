#Описание интерфейса сервиса приема платежей через систему Payin-payout
- [Введение](#Введение)
- [Форма регистрации платежа](#Форма-регистрации-платежа)
- [Фрагмент формы регистрации платежа](#Фрагмент-формы-регистрации-платежа)
- [Форма проверки статуса платежа](#Форма-проверки-статуса-платежа)
- [Фрагмент формы регистрации платежа](#Фрагмент-формы-регистрации-платежа)
- [Проверка информации о платеже](#Проверка-информации-о-платеже)
- [Проверка источника данных](#Проверка-источника-данных)
    - [При формировании счета](#При-формировании-счета)
    - [При отправке оповещения о статусе платежа](#При-отправке-оповещения-о-статусе-платежа)
- [Приложение №1](#Приложение-№1)
    - [Коды типов платежей](#Коды-типов-платежей)
- [Приложение №2](#Приложение-№2)
    - [Обозначения доступных валют для платежей](#Обозначения доступных валют для платежей)


## Введение
Для передачи информации между веб-сайтом продавца и системой Payin-payout используютcя HTML-формы: <br />
1. Форма регистрации платежа - генерируется веб-сайтом продавца для формирования запроса на проведение платежа в  системе Payin-payout и передачи его через веб-браузер покупателя. <br />
2. Форма проверки статуса платежа - генерируется системой Payin-payout для передачи оповещения о платеже на веб-сайт продавца. <br />
3. Используемая кодировка - UTF-8.<br />
Оповещение передается без использования веб-браузера покупателя. 

## Форма регистрации платежа
Эта форма передает запрос с веб-сайта продавца в систему Payin-payout через веб-браузер покупателя. <br /> 
Она должна иметь следующие атрибуты и поля: <br />Обозначение валюты платежа (подробнее см. Приложение №2)
<b>Action</b> — https://lk.payin-payout.net/api/shop<br />
<b>Method</b> - POST<br />
<b>Fields</b> - поля, передаваемые в форме, описаны в таблице ниже:<br />

Название поля | Обязательное | Тип поля | Описание
:--- | :---: |:---:|:---:|:---:
agentId |да| Целое число, от 1 до 999999 | Идентификатор агента (сайта) продавца, на который покупатель должен совершить платеж.
orderId | да | Строка до 50 символов | В этом поле продавец задает уникальный номер заказа (счета) в соответствии со своей системой учета.
agentName |да| Строка |Торговое название агента,выводится пользователю
userName |нет| Строка | Имя (и фамилия) покупателя. Будет показано в счете в качестве покупателя.
amount |да| Дробное число,больше нуля, дробная часть отделяется точкой, два знака после точки.| Сумма платежа, которую продавец желает получить от покупателя.
goods | да | Строка |Название товара (или счета), которые оплачивает клиент
currency |нет | Строка, 3 символа | Обозначение валюты платежа. По умолчанию - RUR. (подробнее см. Приложение №2)
email |да |Строка до 50 символов |Адрес электронной почты покупателя
phone | да | 11 или более цифр | Телефон покупателя в международном формате. Например, для России: 79161234567
preference |нет |Целое число |Способ оплаты, который будет выбран при оплате за товар. см. Приложение №1. Если не будет указан - то будет показана форма со всеми доступными способами оплаты
agentTime | да | Строка в формате «HH:mm:SS dd.MM.yyyy»| Дата создания платежа на стороне агента
limitTime  |нет  |Строка в формате «HH:mm:SS dd.MM.yyyy» | Дата, по истечении которой, заказ считается просроченным. По умолчанию 24 часа
successUrl |нет | Строка длиной до 1024 символов| Адрес на который будет перенаправлен пользователь в случае успешной оплаты
failUrl | нет | Строка длиной до 1024 символов | Адрес на который будет перенаправлен пользователь в случае отмены оплаты
shop_url | нет | Строка длиной до 1024 символов | Адрес на который будет перенаправлен пользователь в случае нажатии им на ссылку "Вернуться в магазин"
addInfo_N | нет | Строка длиной до 1024 символов | Все поля формы, имеющие в названии префикса "addInfo_N"(где N порядковый номер),обрабатываются системой Payin-payout автоматически и передаются на сайт продавца. (Кодировка UTF-8)
sign | да |Строка | Контрольная подпись оповещения осоздании платежа, которая используется для проверки однозначной идентификации отправителя.

* Если в процессе регистрации платежа произошла ошибка и в запросе не указан failUrl
то система выводит ошибку 500, иначе к строке переадресация failUrl добавляется 
query параметр error с описанием ошибки.
* Если процесс регистрации платежа прошел успешно и указан successUrl, то к строке
переадресации successUrl добавляется query параметр paymentId с номером заказа в 
системе Payin-payout. <br/>

### Фрагмент формы регистрации платежа

```

<form action="URL регистрации платежа" method="POST">
 <input type="hidden" name="agentId" value="8686"> 
 <input type="hidden" name="agentName" value="Superstore"> 
 <input type="hidden" name="orderId" value="87876"> 
 <input type="hidden" name="amount" value="166.70"> 
 <input type="hidden" name="email" value="user@example.ru"> 
 <input type="hidden" name="phone" value="79090000001"> 
 <input type="hidden" name="agentTime" value="20:35:67 01.01.2010"> 
 <input type="hidden" name="goods" value="Notebook"> 
 <input type="hidden" name="currency" value="RUR"> 
 <input type="hidden" name="preference" value="1"> 
 <input type="hidden" name="successUrl" value="http://example.ru/success.html"> 
 <input type="hidden" name="failUrl" value="http://example.ru/fail.html"> 
 <input type="hidden" name="sign" value="f849a1c57cccb372ec4a3a2e04d2feba">
 <input type="hidden" name="addInfo_1" value="addinf1"> 
 ...
 ...
 <input type="submit" value="Оплатить "> 
</form>

 ```

### Форма проверки статуса платежа
Эта форма оповещения о платеже, отправляемая продавцу при изменении статуса платежа.
Она имеет следующие атрибуты и поля:

**Action** - URL информирования о статусе платежа (предоставляется клиентом Payin-payout) <br/>
**Method** - POST <br/>
**Fields** - поля, передаваемые в форме, описаны в таблице ниже: <br/>

Название поля | Обязательное | Тип поля | Описание
:---|:---:|:---:|:---:
agentId | да | Целое число, от 1 до 999999 | Идентификатор агента (сайта) продавца, на который покупатель должен совершить платеж.
orderId | да | Строка до 50 символов | В этом поле продавец задает уникальный номер заказа (счета) в соответствии со своей системой учета.
paymentId | да | Целое число большее нуля | В этом поле передается номер покупки в системе EKO (Номер транзакции).
amount | да | Дробное число, больше нуля, дробная часть отделяется | Сумма платежа, которую продавец желает получить от покупателя. точкой, два знака после точки.
currency | нет | Строка, 3 символа | Обозначение валюты платежа (подробнее см. Приложение №2)
phone | да | 11 или более цифр | Телефон покупателя в международном формате. Например, для России: 79161234567
preference | да | Целое число | Способ оплаты, который будет выбран при оплате за товар. см. Приложение №1
paymentStatus | да | Целое число | Статус платежа может иметь следующие значения : <br/> 1 — Прошел успешно <br/> 2 — Фатальная ошибка, платеж не  прошел <br/> 3 — В очереди <br/>
paymentDate | да | Строка в формате «HH:mm:SS dd.MM.YYYY» | Дата и время реального прохождения платежа в системе Payin-payout
goods | Да | Строка | Описание предоставляемой услуги или товара
agentName | Да | Строка | Название агента, понятное плательщику, например брэнд или товарный знак под которым работает агент
sign | да | Строка | Контрольная подпись оповещения о выполнении платежа, которая используется для проверки целостности полученной информации и однозначной идентификации отправителя.
comment | нет | Строка длиной до 1024 символов | Комментарий к платежу (Например,  при оплате по смс — это текст отправленного сообщения), текст  закодирован алгоритмом url encoding.
addInfo_N | нет | Строка длиной до 1024 символов | Все поля, переданные с веб-сайта продавца в "Форме регистрации  платежа"

### Фрагмент формы регистрации платежа

```

<form action="URL информирования о статусе платежа" method="POST">
 <input type="hidden" name="agentId" value="8686"> 
 <input type="hidden" name="paymentId" value="12345678">
 <input type="hidden" name="orderId" value="87876"> 
 <input type="hidden" name="amount" value="166.70"> 
 <input type="hidden" name="phone" value="79090000001"> 
 <input type="hidden" name="currency" value="RUR"> 
 <input type="hidden" name="preference" value="1"> 
 <input type="hidden" name="goods" value="Рога, 10 кг">
 <input type="hidden" name="agentName" value="Рога и Копыта (TM)">
 <input type="hidden" name="paymentStatus" value="1">
 <input type="hidden" name="paymentDate" value="13:12:03 10.01.2010">
 <input type="hidden" name="sign" value="f849a1c57c66b372ec4a3a2e04d2feba">
 <input type="hidden" name="addInfo_1" value="addinfoxxxxxxxx"> 
 ...
 ...
</form>

```
### Проверка информации о платеже
При выполнении платежа система Payin-payout высылает оповещение о платеже через "Форму проверки статуса платежа" на URL информирования о статусе платежа, указанный
продавцом. Сообщение считается доставленным, если страница обрабатывающая уведомление, вернет 'OK', иначе оповещение о выполненном платеже будет повторяться.
Мы рекомендуем вам проверить данные, полученные через "Форму оповещения о платеже": <br/>

1. Проверить, действительно ли данные переданы от системы Payin-payout
2. Проверить, не исказились ли данные в процессе передачи
3. Проверить сумму платежа. (!) Сумма платежа означает на сколько денег уже товар был оплачен и в случае частичной оплаты будет идти по нарастающей. Например пользователь создал счет на 200 руб. При пополнении сначала на 30, потом на 100 и затем на 70 будет выслано три уведомления на сумму 30, 130 и 200 рублей соответственно. Необходимо предусмотреть корректную обработку данного типа оповещений.
4. Проверить идентификатор агента (сайта) продавца

### Проверка источника данных
#### При формировании счета
При формировании счета в форме регистрации платежа, продавец передает системе Payin-payout реквизиты платежа и контрольную подпись, позволяющую однозначно идентифицировать
агента.<br/>
При формировании контрольной подписи, продавец "склеивает" значения полей разделяя их символом “#”, в одну строку в следующем порядке:

1. Идентификатор агента (продавца) (agentId);
2. Уникальный номер заказа (счета) (orderId)
3. Дата создания платежа (agentTime)
4. Сумма платежа (amount)
65. Телефон покупателя (phone)

Пример :
 ````
MD5(8686# 87876#13:12:03 10.01.2010#166.70#79090000001#MD5(secret))
````
**secret** — секретная фраза агента, сохраняется через личный кабинет агента MD5 формируется последовательность из 32-х шестнадцатеричных цифр в соответствии с широко распространенным алгоритмом Message Digest 5 (MD5). <br/>
#### При отправке оповещения о статусе платежа
Высылая оповещение о проведении платежа, система Payin-payout передает реквизиты платежа и контрольную подпись, позволяющую проверять неизменность передаваемых данных. <br/>
Контрольная подпись данных о платеже позволяет продавцу проверять источник данных, переданных на продавцу. <br/>
При формировании контрольной подписи система Payin-payout "склеивает" значения полей разделяя  их “#”, передаваемых "Форма проверки статуса платежа", в одну строку в следующем порядке:<br/>

1. Идентификатор агента (продавца) (agentId);
2. Уникальный номер заказа (счета) (orderId)
3. Номер покупки в системе EKO (paymentId)
4. Сумма платежа (amount)
5. Телефон покупателя (phone)
6. Статус платежа (paymentStatus)
7. Дата и время реального прохождения платежа (paymentDate)
8. Секретный ключ (пароль) агента

Пример :
 ````
MD5(8686# 87876# 12345678# 166.70# 79090000001#1# 13:12:03 10.01.2010#MD5(secret))
`````
MD5 формируется последовательность из 32-х шестнадцатеричных цифр в соответствии с широко распространенным алгоритмом Message Digest 5 (MD5). <br/>

### Приложение №1

#### Коды типов платежей

Код типа платежа | Описание | Примечания
:---|:---:|:---
0 | Сервис не указан | в случае уведомления об ошибке
1 | Со счета Payin-payout | Нужно иметь учетную запись в системе
2 | Терминалы "QIWI" | Отображаются инструкции по оплате
5 | Банковский перевод по РФ | Показывает форму счета
6 | Яндекс.деньги | В настоящий момент может быть недоступно
7 | Банковский перевод SWIFT | Показывает форму счета
8 | Киви-кошелек | В настоящий момент недоступно
13 | RBK Money | В настоящий момент недоступно
14 | Единая касса (WalletOne)
15 | Paypal
22 | Пластиковые карты (high risk) | Еще один способ оплаты с помощью пластиковых карт
124 | Терминалы Элекснет | Отображаются инструкции по оплате
125 | Пластиковые карты | Могут быть недоступны. Альтернативно можно использовать 22
126 | Альфа-клик | В параметре addInfo_1 магазин может передавать идентификатор плательщика в Alfa-Click. Если не будет указан, то на форме счета пользователю будет предложено его ввести
127 | SMS Оплата (МТС, Мегафон, Теле 2) |  Отображаются инструкции по оплате.
128 | SMS Оплата (Билайн) |  Отображаются инструкции по оплате. В случае, если оператор не Билайн будет ошибка. Только по РФ
129 | Веб мани
130 | Салоны связи Евросеть и салоны МТС | Отображаются инструкции по оплате

### Приложение №2

#### Обозначения доступных валют для платежей

Обозначение валюты | Название
:---|:---
RUR | Российский рубль
EUR | Евро
USD | Доллар США
GBP | Фунт стерлингов
UAH | Украинская гривна
