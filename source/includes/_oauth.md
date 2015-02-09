
# OAuth

mserver поддерживает [протокол OAuth 2.0](http://oauth.net/2/).
Зарегистрированное клиентское приложение может получить [токен доступа](#token-dostupa) (access token) по одному из поддерживаемых сценариев (flow):

* Веб приложения с бэкэндом должны использовать сценарий с кодом авторизации ([authorization code flow](http://tools.ietf.org/html/rfc6749#section-1.3.1)).
* Мобильный клиент, который сейчас использует BASIC аутентификацию паролем может использовать сценарий с паролем пользователя 
([password flow](http://tools.ietf.org/html/rfc6749#section-1.3.3)).

## Токен доступа

После успешной OAuth авторизации клиентское приложение получает токен доступа вида 
`b48a991e-e010-4329-9817-f8389a774c45`, который может использовать для обращения к API от имени пользователя кошелька. 

Время жизни токена зависит от настроек [регистрации](#testovye-prilozheniya) приложения в mserver. 
С токеном связан набор [разрешений](#razresheniya), предоставленных пользователем приложению. 
При попытке выполнить неразрешенное действие клиентское приложение получит статус HTTP 403, `insufficient_scope` в поле `meta.error` ответа и идентификатор разрешения, которого не хватило, в поле `meta.details`.

> Успешная аутентификация токеном доступа

```shell
$ curl -H "Authorization: Bearer b48a991e-e010-4329-9817-f8389a774c45" https://www.synq.ru/mserver2-dev/v1/wallet
```

```json
{
  "meta" : {
    "code" : 200,
    "urgent_data" : {
      "amount" : 10098.98
    }
  },
  "data" : {
    "phone" : "+79261111111",
    "amount" : 10098.98,
    "lock_message" : "t",
    "name" : "Васян Петров",
    "verified" : true,
    "person_status" : "data_verified"
  }
}
```

> Попытка выполнить неразрешенное пользователем действие


```shell
curl -H "Authorization: Bearer b48a991e-e010-4329-9817-f8389a774c45" https://www.synq.ru/mserver2-dev/v1/wallet/person
```
```json
{
  "meta" : {
    "code" : 403,
    "error" : "insufficient_scope",
    "details" : "person.read"
  }
}
```

## Сценарий с кодом авторизации

1. Клиентское приложение перенаправляет клиента на URL авторизации вида `https://www.synq.ru/mserver2-dev/oauth/authorize?response_type=code&client_id=mbank_web&scope=wallet.read payments.read&redirect_uri=https://bestmt.ru/test?ok=1`

2. Происходит перенаправление на страницу логина https://www.synq.ru/mserver2-dev/login

3. Пользователь кошелька вводит логин и пароль и нажимает "Войти"

4. Пользователь кошелька выбирает, какие из запрошенных разрешений он согласен предоставить приложению и нажимает "Продолжить"

5. Происходит перенаправление на страницу с кодом авторизации https://bestmt.ru/test?ok=1&code=QnHgkv

6. Бэкэнд клиентского приложения обменивает код авторизации на [токен доступа](#token-dostupa) POST запросом к сервису токенов mserver `/oauth/token`.

### Параметры URL авторизации
 
 * `response_type` - обязательно, фиксированное значение, равное `code`
 * `client_id` - обязательно, [зарегистрированный](#testovye-prilozheniya) в mserver ID приложения, которое запрашивает доступ
 * `scope` - опционально, перечень запрашиваемых [разрешений](#razresheniya), разделенный пробелами, если ничего не передано, то будут запрошены все разрешения
 * `redirect_uri` - опционально, URI на который пользователь будет перенаправлен в случае успешной авторизации, должен быть [зарегистрирован](#testovye-prilozheniya) в mserver

### Параметры запроса к сервису токенов для authorization code flow

* `grant_type` - обязательно, фиксированное значение `authorization_code`
* 'code' - обязательно, код авторизации, полученный из URL перенаправления
* `redirect_uri` - опционально, если был задан параметр redirect_uri в URL авторизации
* `client_id` - обязательно, [зарегистрированный](#testovye-prilozheniya) в mserver ID приложения, которое запрашивает доступ


> Обмен кода авторизации на токен доступа 

```shell
curl -H "Accept: application/json" -u mbank_web:topsecret https://www.synq.ru/mserver2-dev/oauth/token -d "grant_type=authorization_code&code=xT53tR&redirect_uri=https://bestmt.ru/test?ok=1&client_id=mbank_web"
```

```json
{
  "meta" : {
    "code" : 200,
    "urgent_data" : {
      "amount" : 10098.98
    }
  },
  "data" : {
    "access_token" : "b48a991e-e010-4329-9817-f8389a774c45",
    "token_type" : "bearer",
    "expires_in" : 2147483253,
    "scope" : "payments.read wallet.read"
  }
}
```





## Сценарий с передачей пароля

Для замены старой схемы аутентификации в мобильном приложении кошелька может быть использован сценарий password flow, при котором бэкэнд приложения обращается к mserver,
чтобы обменять учетные данные приложения и пользователя кошелька (client_id, client_secret, username, password) на [токен доступа](#token-dostupa).

### Параметры запроса к сервису токенов для password flow

* `grant_type` - обязательно, фиксированное значение `password`
* `scope` - опционально, перечень запрашиваемых [разрешений](#razresheniya), разделенный пробелами, если ничего не передано, то будут запрошены все разрешения
* `username` - обязательно, имя пользователя кошелька (номер телефона в международном формате)
* `password` - обязательно, пароль пользователя кошелька

Учетные данные (`client_id` и `client_secret`) [зарегистрированного](#testovye-prilozheniya) клиентского приложения, от имени которого запрашивается токен доступа, передаются BASIC аутентификацией.

> Обмен пароля пользователя кошелька на токен доступа

```shell
curl -H "Accept: application/json" -u mbank:secret https://www.synq.ru/mserver2-dev/oauth/token -d "grant_type=password&scope=cards.read&username=%2B79201111111&password=password"
```

```json
{
  "meta" : {
    "code" : 200
  },
  "data" : {
    "access_token" : "6a1c96df-662c-4d1d-b046-a7a4ad14b159",
    "token_type" : "bearer",
    "expires_in" : 3599,
    "scope" : "cards.read"
  }
}
```

## Разрешения

| Разрешение      | Описание                      | Разрешенные запросы API |
| :-------------- | :---------------------------- | :-----------------------|
| wallet.read     | Просмотр кошелька             | GET /v1/wallet          |
| person.read     | Просмотр персональных данных  | GET /v1/wallet/person   |
| person.modify   | Задание персональных данных   | POST /v1/wallet/person  |
| cards.read      | Просмотр сохраненных карт     | GET /v1/cards           |
| cards.modify    | Сохранение/удаление карты     | POST DELETE /v1/cards   |
| payments.read   | Просмотр истории платежей     | GET /v1/payments        |
| payments.modify | Совершение платежа            | POST /v1/payments       |

## Тестовые приложения

| client_id        | client_secret  | Разрешенные сценарии | URL перенаправления |  Время жизни токена, секунды |
| :----------------|:---------------|:-------------------- |:--------------------|:-----------------------------|
| mbank            | secret         | password             |                     | 3600                         |
| mbank_web        | topsecret      | authorization_code   | http://refill.dev http://refill.nebo15.me http://mbank.dev http://schet.dev http://schet.nebo15.me http://schet.ru | 2147483647 |
| mbank_storefront | oklol          | client_credentials   | http://refill.dev http://refill.nebo15.me http://mbank.dev http://schet.dev http://schet.nebo15.me http://schet.ru | 2147483647 |
| bov              | secret         | password             |                     | 3600                         |
