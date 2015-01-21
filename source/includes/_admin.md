# Административное API


## Аутентификация

На тестовой системе созданы 2 пользователя от имени которых возможен вызов административного API.

| Логин      | Пароль  | Роли пользователя |
| :--------  |:------- |:------------------|
| user       | user    | user              |
| admin      | admin   | admin             |

## Получение всех кошельков проекта

### Многопроектность

* `project_id` - ID проекта, в котором будет производиться поиск. Доступен только суперадминистраторам.

### Постраничная навиция

* `page` - номер (начиная с 0) страницы, которую запрашивает клиент, опционально, по умолчанию 0
* `size` - размер страницы, которую запрашивает клиент, опционально, по умолчанию 35

### Параметры

* `order_by` - поле для сортировки. Можно указать несколько полей через запятую, например: `amount,created_at`. Можно указать направление через запятую, например: `amount,desc`
* `group_by` - поле для группировки
* `tick` (day|month) - выбор разреза при группировке (день, месяц). По умолчанию - день.

### Поля ответа:

- `phone` - номер телефона, к которому привязан кошелек
- `amount` - остаток на балансе кошелька
- `enabled` - false если кошелек заблокирован, иначе - true
- `active` - true если кошелек активирован через СМС-код
- `role` - роль пользователя (всегда равно user)
- `created_at` - дата регистрации пользователя
- `person.*` - идентификационные данные пользователя
- `statistics.payments.lifetime.turnover` - оборот по кошельку за все время
- `statistics.payments.lifetime.in_turnover` - оборот по транзакциям типа in за все время
- `statistics.payments.lifetime.out_turnover` - оборот по транзакциям типа out за все время
- `statistics.payments.lifetime.p2p_turnover` - оборот по транзакциям типа p2p за все время
- `statistics.payments.lifetime.count` - количество транзакций за все время
- `statistics.payments.lifetime.in_count` - количество транзакций типа in за все время
- `statistics.payments.lifetime.out_count` - количество транзакций типа out за все время
- `statistics.payments.lifetime.p2p_count` - количество транзакций типа out за все время
- `statistics.payments.last_month.turnover` - оборот по кошельку за последний месяц
- `statistics.payments.last_month.in_turnover` - оборот по транзакциям типа in за последний месяц
- `statistics.payments.last_month.out_turnover` - оборот по транзакциям типа out за последний месяц
- `statistics.payments.last_month.p2p_turnover` - оборот по транзакциям типа p2p за последний месяц
- `statistics.payments.last_month.count` - количество транзакций за последний месяц
- `statistics.payments.last_month.in_count` - количество транзакций типа in за последний месяц
- `statistics.payments.last_month.out_count` - количество транзакций типа out за последний месяц
- `statistics.payments.last_month.p2p_count` - количество транзакций типа out за последний месяц
- `statistics.cards.count` - количество привязанных и удаленных карт
- `statistics.cards.active_count` - количество привязанных активных карт


### Поля для сортировки:

- `statistics.cards.*` - по любому полю со статистики количества карт
- `created_at` - по дате регистрации
- `amount` - по остаткам

### Фильтры:

- `ip_address` - по ip адресу
- `created_before`
- `created_after`
- `person[given_name]` `person[family_name]` `person[patronymic_name]` - по ФИО, поиск полного совпадения или совпадения в начале
- `person[status]` - по статусу идентификации
- `phone` - по номеру телефона, поиск полного совпадения или совпадения в начале
- `card_number` - по бин+номер карты - чтобы искать пользователей, у которых была привязана эта же карта, поиск любых совпадений внутри номера
- `card_id` - по ID карты в IPSP
- `amount_from`
- `amount_to`
- `active` - по статусу активации (true|false)

### Группировки:

Можно задать поле, по которому будет группировка, в таком случае возвращаемый результат заменяется на агрегированную статистику. Поля для группировок:
- `created_at` - вернет количество новых регистрацией за каждый `tick`

```shell
$ curl -uadmin:admin "https://www.synq.ru/mserver2-dev/admin/wallets?family_name=арсен&active=true&sort=payment_count,desc"
```

```json
{
  "meta" : {
    "page" : {
      "total_elements" : 4
    },
    "code" : 200
  },
  "data" : [ {
    "phone" : "+380503839001",
    "amount" : 8598.17,
    "enabled" : true,
    "active" : true,
    "role" : "user",
    "created_at" : "2014-08-20T15:10:25.943Z",
    "person" : {
    	"family_name" : "Арсеньев",
    	"given_name" : "Алексей",
    	"patronymic_name" : "Александрович",
    	"passport_series_number" : "2202655885",
    	"passport_issued_at" : "2012-02-27",
    	"itn" : "330500938709",                       
    	"ssn" : "11223344595",                        
    	"status" : "data_entered"
    },
    "statistics": {
      "payments": {
        "lifetime": {
          "turnover" : 8,
          "in_turnover" : 8,
          "out_turnover" : 8,
          "p2p_turnover" : 8,
          "count": 8,
          "in_count" : 8,
          "out_count" : 8,
          "p2p_count" : 8,
        },
        "last_month": {
          "turnover" : 8,
          "in_turnover" : 8,
          "out_turnover" : 8,
          "p2p_turnover" : 8,
          "count": 8,
          "in_count" : 8,
          "out_count" : 8,
          "p2p_count" : 8,
        }
      },
      "cards": {
        "count": 4,
        "active_count": 2
      }
    }
  } ]
}
```

## Загрузка кошелька
### Многопроектность

* `project_id` - ID проекта, в котором будет производиться поиск. Доступен только суперадминистраторам.

### Поля ответа:

- `phone`
- `amount`
- `enabled`
- `active`
- `role`
- `created_at`
- `person.*` - идентификационные данные пользователя
- `cards.*` - данные по картам прикреплённым к кошельку
- `cards.state` - created | pending | active | failed | deleted | used - состояние карты
- `cards.3ds` - unknown | success | failed | none - вид 3D Secure
- `cards.last_payment_status` - created | processing | completed | declined - статус последнего платежа
- `statistics.payments.lifetime.turnover` - оборот по кошельку за все время
- `statistics.payments.lifetime.in_turnover` - оборот по транзакциям типа in за все время
- `statistics.payments.lifetime.out_turnover` - оборот по транзакциям типа out за все время
- `statistics.payments.lifetime.p2p_turnover` - оборот по транзакциям типа p2p за все время
- `statistics.payments.lifetime.count` - количество транзакций за все время
- `statistics.payments.lifetime.in_count` - количество транзакций типа in за все время
- `statistics.payments.lifetime.out_count` - количество транзакций типа out за все время
- `statistics.payments.lifetime.p2p_count` - количество транзакций типа out за все время
- `statistics.payments.last_month.turnover` - оборот по кошельку за последний месяц
- `statistics.payments.last_month.in_turnover` - оборот по транзакциям типа in за последний месяц
- `statistics.payments.last_month.out_turnover` - оборот по транзакциям типа out за последний месяц
- `statistics.payments.last_month.p2p_turnover` - оборот по транзакциям типа p2p за последний месяц
- `statistics.payments.last_month.count` - количество транзакций за последний месяц
- `statistics.payments.last_month.in_count` - количество транзакций типа in за последний месяц
- `statistics.payments.last_month.out_count` - количество транзакций типа out за последний месяц
- `statistics.payments.last_month.p2p_count` - количество транзакций типа out за последний месяц
- `statistics.cards.count` - количество привязанных и удаленных карт
- `statistics.cards.active_count` - количество привязанных активных карт

```shell
$ curl -uuser:user "https://www.synq.ru/mserver2-dev/admin/wallets/%2B79260000006"
```

```json
{
  "meta":{
    "code": 200
  },
  "data":{
    "phone":"+79260000006",
    "amount":65572.14,
    "enabled":true,
    "active":true,
    "role":"user",
    "created_at":"2014-06-01T12:43:52.876Z",
    "person" : {
      "family_name" : "Арсеньев",
      "given_name" : "Алексей",
      "patronymic_name" : "Александрович",
      "passport_series_number" : "2202655885",
      "passport_issued_at" : "2012-02-27",
      "itn" : "330500938709",                       
      "ssn" : "11223344595",                        
      "status" : "data_entered"
    },
    "cards":[ {
      "card_id" : 14,
      "state" : "used",
      "title" : "541715******2399",
      "type" : "MasterCard",
      "3ds" : "none",
      "lifetime_turnover" : 245435.56,
      "card_holder_name" : "Ivanov Ivan",
      "last_payment_status" : "completed"
    } ],
    "limits":{
      "in_amount_limit":15000,
      "out_amount_limit":15000,
      "wallet_amount_limit":15000,
      "monthly_in_turnover_limit":40000,
      "monthly_out_turnover_limit":40000,
      "monthly_p2p_turnover_limit":40000,
      "active_cards_limit":10,

      "in_amount_limit_available":15000,
      "out_amount_limit_available":15000,
      "wallet_amount_limit_available":15000,
      "active_cards_limit_available": 10
    },
    "statistics": {
      "payments": {
        "lifetime": {
          "turnover" : 8,
          "in_turnover" : 8,
          "out_turnover" : 8,
          "p2p_turnover" : 8,
          "count": 8,
          "in_count" : 8,
          "out_count" : 8,
          "p2p_count" : 8,
        },
        "last_month": {
          "turnover" : 8,
          "in_turnover" : 8,
          "out_turnover" : 8,
          "p2p_turnover" : 8,
          "count": 8,
          "in_count" : 8,
          "out_count" : 8,
          "p2p_count" : 8,
        }
      },
      "cards": {
        "count": 4,
        "active_count": 2
      }
    }
  }
}
```

## Получение кода активации кошелька

```shell
$ curl -uuser:user "https://www.synq.ru/mserver2-dev/admin/wallets/%2B12345657367/secure_code"
```

```json
{
  "meta" : {
    "code" : 200
  },
  "data" : "2899066"
}
```

## Блокировка кошелька

### Параметры

* `message` - сообщение для пользователя кошелька, объясняющее причину блокировки и как дальше жить
* `reason` - сообщение для сотрудников проекта о причине блокировки (не видно клиенту)

```shell
$ curl -uuser:user  -H 'Content-type:application/json' --data '{"message": "Заблокирован до выяснения.", "reason": "Клиент - кардер." }' "https://www.synq.ru/mserver2-dev/admin/wallets/%2B12345657367/disable"
```

```json
{
  "meta" : {
    "code" : 200
  },
  "data" : {
    "phone" : "+12345657367",
    "amount" : 10000,
    "reset_password" : false,
    "lock_message" : "Заблокироваан до выяснения.",
    "verified" : false,
    "enabled" : false,
    "active" : true,
    "lock_reason" : "Клиент - кардер."
    "locked_at" : "2014-08-14T16:46:42.122Z"
  }
}
```

## Разблокировка кошелька

```shell
$ curl -uuser:user -X POST "https://www.synq.ru/mserver2-dev/admin/wallets/%2B12345657367/enable"
```

```json
{
  "meta" : {
    "code" : 200
  },
  "data" : {
    "phone" : "+12345657367",
    "amount" : 10000,
    "reset_password" : false,
    "verified" : false,
    "enabled" : true,
    "active" : true
  }
}
```

## Получение платежей кошелька

### Параметры

* `wallet` - обязательный параметр, телефон кошелька чьи платежи мы хотим видеть
* `type` - фильтр по типу платежа, допускается указание нескольких значений разделенный запятыми, опционально
* `status`- фильтр по статусу платежа, допускается указание нескольких значений разделенный запятыми, опционально
* `service` - идентификатор сервиса, опционально
* `amount_from`и `amount_to` - границы диапазона сумм платежей, опционально
* `date_from` и `date_to` - границы диапазона дат создания платежей, опционально
* `page` - номер (начиная с 0) страницы, которую запрашивает клиент, опционально, по умолчанию 0
* `size` - размер страницы, которую запрашивает клиент, опционально, по умолчанию 35

```shell
$ curl -uuser:user "https://www.synq.ru/mserver2-dev/admin/wallets/%2B79260000006/payments?type=out&status=created&amount_from=0.5&amount_to=1&date_from=2014-08-01T12:10:15.525Z&date_to=2014-08-11T00:00:00.00Z&service=1"
```

```json
{
  "meta" : {
    "page" : {
      "total_elements" : 1
    },
    "code" : 200
  },
  "data" : [ {
    "id" : 1401089240212,
    "client_payment_id" : "c131a7e2-c553-4295-867e-1023359bee28",
    "amount" : 1,
    "total" : 4.01,
    "created_at" : "2014-08-01T12:20:15.525Z",
    "status" : "created",
    "type" : "out",
    "service" : {
      "id" : 1,
      "name" : "Мегафон"
    },
    "parameters" : [ {
      "code" : "phoneNumber",
      "name" : "№ телефона (10 цифр)",
      "value" : "9157101280"
    } ],
    "outbound" : {
      "id" : 35,
      "code" : "tpr_out",
      "name" : "ООО ТПР (провайдер)"
    },
    "wallet" : {
      "phone" : "+79260000006"
    }
  } ]
}
```
## Отчет об обороте кошелька по дням

```shell
$ curl -uuser:user "https://www.synq.ru/mserver2-dev/admin/wallets/%2B79260000006/turnover?from=2014-05-01&to=2014-06-03"
```

```json
{
  "meta" : {
    "code" : 200
  },
  "data" : [ {
    "tick" : "2014-06-01",
    "amount" : 125477.16
  }, {
    "tick" : "2014-06-02",
    "amount" : 0
  }, {
    "tick" : "2014-06-03",
    "amount" : 0
  } ]
}
```

## Получение отчёта по платежам

### Параметры

Все параметры опциональны

* `wallet` - телефон кошелька, чьи платежи мы хотим видеть
* `type` - тип платежа
* `status`- статус платежа
* `service_name` - полное или частичное имя сервиса
* `amount_from` и `amount_to` - границы диапазона сумм платежей
* `date_from` и `date_to` - границы диапазона дат создания платежей
* `ipsp_payment_id` - идентификатор платежа из IPSP
* `page` - номер (начиная с 0) страницы, которую запрашивает клиент, по умолчанию 0
* `size` - размер страницы, которую запрашивает клиент, по умолчанию 20
* `sort` - поле сортировки через запятую может следовать направлене

```shell
$ curl -uuser:user "https://www.synq.ru/mserver2-dev/admin/payments?service_name=mts&type=out&status=created&amount_from=0&amount_to=100000&date_from=2014-01-01T12:10:15.525Z&date_to=2014-12-01T00:00:00.00Z&sort=amount,desc&size=1"
```

```json
{
  "meta" : {
    "page" : {
      "total_elements" : 14
    },
    "code" : 200
  },
  "data" : [ {
    "id" : 1401089244704,
    "client_payment_id" : "409c1e06-5faa-11e4-a61c-b88d12284ddc",
    "amount" : 12000,
    "total" : 12070,
    "created_at" : "2014-10-29T20:29:16.495Z",
    "status" : "created",
    "type" : "out",
    "service" : {
      "id" : 15,
      "name" : "MTS Украина"
    },
    "parameters" : [ {
      "code" : "phoneNumber",
      "name" : "№ телефона (9-10 цифр)",
      "value" : "0509244512"
    } ],
    "outbound" : {
      "id" : 35,
      "code" : "tpr_out",
      "name" : "ООО ТПР (провайдер)"
    },
    "wallet" : {
      "phone" : "+79270000001",
      "amount" : 8496.32,
      "verified" : false,
      "ip" : "37.110.42.197"
    }
  } ]
}
```

> Пример фильтра по кошельку и IP

```shell
$ curl -uuser:user "https://www.synq.ru/mserver2-dev/admin/payments?wallet=%2B380935895452&payment_ip=127.0.0.1&size=1"
```

```json
{
  "meta" : {
    "page" : {
      "total_elements" : 2
    },
    "code" : 200
  },
  "data" : [ {
    "id" : 1401089245266,
    "client_payment_id" : "96c280f4-8e2b-40a1-b250-806bb4a4b9f1",
    "amount" : 10000,
    "total" : 10000,
    "created_at" : "2014-11-04T13:02:09.409Z",
    "processed_at" : "2014-11-04T13:02:13.619Z",
    "status" : "completed",
    "type" : "p2p",
    "wallet" : {
      "phone" : "+380935895452",
      "amount" : 0,
      "name" : "Иван Иванов",
      "verified" : true,
      "ip" : "127.0.0.1"
    },
    "destination" : {
      "phone" : "+79555555555"
    },
    "direction" : "out",
    "payment_ip" : "127.0.0.1"
  } ]
}
```

> Пример фильтра по ipsp_payment_id

```shell
$ curl -uuser:user "https://www.synq.ru/mserver2-dev/admin/payments?ipsp_payment_id=6143708"
```

```json
{
  "meta" : {
    "code" : 200,
    "page" : {
      "total_elements" : 1
    }
  },
  "data" : [ {
    "id" : 1401089245752,
    "client_payment_id" : "6ea16c52-669e-11e4-86b1-3c07542cf2f2",
    "amount" : 42,
    "total" : 42,
    "created_at" : "2014-11-07T16:52:17.990Z",
    "processed_at" : "2014-11-07T16:52:21.791Z",
    "status" : "completed",
    "type" : "in",
    "inbound" : {
      "id" : 62,
      "code" : "ipsp_in",
      "name" : "ООО ИПСП (агент)"
    },
    "card" : {
      "state" : "used",
      "title" : "541715******2399",
      "type" : "MasterCard"
    },
    "wallet" : {
      "phone" : "+79270000001",
      "amount" : 7462.54,
      "level" : "anonymous",
      "name" : "Пиэчпий Мбанков",
      "verified" : true,
      "person_status" : "data_verified",
      "enabled" : true,
      "active" : true,
      "role" : "user",
      "created_at" : "2014-10-29T16:33:14.045Z"
    },
    "payment_ip" : "81.95.134.13",
    "card_payment" : {
      "amount" : 4200,
      "ipsp_payment_id" : 1401089245752,
      "type" : "SALE",
      "date" : "2014-11-07T16:52:19.804Z",
      "product_id" : 1721,
      "currency" : "RUB",
      "card_holder_name" : "TESTER TESTEROV",
      "exp_year" : 2014,
      "exp_month" : 11,
      "remote_ip" : "81.95.134.13",
      "user_ip" : "81.95.134.13",
      "card_number_mask" : "541715******2399",
      "card_type" : "MASTER_CARD",
      "recurring" : false,
      "steps" : [ {
        "date" : "2014-11-07T16:52:19.867Z",
        "status" : "PASSED_0",
        "type" : "ANTIFRAUD"
      }, {
        "eci" : "06",
        "date" : "2014-11-07T16:52:21.571Z",
        "status" : "PASSED_0",
        "type" : "BANK",
        "auth_id_response" : "411421",
        "date_local_trans" : "2014-11-07T13:52:21.000Z",
        "response_code" : "APPROVED_00"
      }, {
        "date" : "2014-11-07T16:52:19.843Z",
        "status" : "PASSED_0",
        "type" : "PAYMENT_INPUT"
      } ]
    }
  } ]
}
```

## Поиск по платежам IPSP

### Параметры

Все параметры опциональны

* `wallet` - телефон кошелька, чьи платежи мы хотим видеть
* `type` - тип платежа
* `status`- статус платежа
* `amount_from` и `amount_to` - границы диапазона сумм платежей
* `date_from` и `date_to` - границы диапазона дат создания платежей
* `ipsp_payment_id` - идентификатор платежа из IPSP
* `page` - номер (начиная с 0) страницы, которую запрашивает клиент, по умолчанию 0
* `size` - размер страницы, которую запрашивает клиент, по умолчанию 20
* `sort` - поле сортировки через запятую может следовать направление

```shell
$ curl -uuser:user "https://www.synq.ru/mserver2-dev/admin/payments/ipsp?service_name=mts&type=out&status=created&amount_from=0&amount_to=100000&date_from=2014-01-01T12:10:15.525Z&date_to=2014-12-01T00:00:00.00Z&sort=amount,desc&size=1"
```

```json
{
  "meta" : {
    "page" : {
      "total_elements" : 14
    },
    "code" : 200
  },
  "data" : [ {
    "amount" : 4200,
    "ipsp_payment_id" : 1401089245752,
    "type" : "SALE",
    "date" : "2014-11-07T16:52:19.804Z",
    "product_id" : 1721,
    "currency" : "RUB",
    "card_holder_name" : "TESTER TESTEROV",
    "exp_year" : 2014,
    "exp_month" : 11,
    "remote_ip" : "81.95.134.13",
    "user_ip" : "81.95.134.13",
    "card_number_mask" : "541715******2399",
    "card_type" : "MASTER_CARD",
    "recurring" : false,
    "steps" : [ {
      "date" : "2014-11-07T16:52:19.867Z",
      "status" : "PASSED_0",
      "type" : "ANTIFRAUD"
    }, {
      "eci" : "06",
      "date" : "2014-11-07T16:52:21.571Z",
      "status" : "PASSED_0",
      "type" : "BANK",
      "auth_id_response" : "411421",
      "date_local_trans" : "2014-11-07T13:52:21.000Z",
      "response_code" : "APPROVED_00"
    }, {
      "date" : "2014-11-07T16:52:19.843Z",
      "status" : "PASSED_0",
      "type" : "PAYMENT_INPUT"
    } ]
  } ]
}
```

## Отчет об остатке кошелька по дням

```shell
$ curl -uuser:user "https://www.synq.ru/mserver2-dev/admin/wallets/%2B79260000006/balance?from=2014-07-11&to=2014-07-13"
```

```json
{
  "meta" : {
    "code" : 200
  },
  "data" : [ {
    "tick" : "2014-07-11",
    "amount" : 55572.14
  }, {
    "tick" : "2014-07-12",
    "amount" : 55572.14
  }, {
    "tick" : "2014-07-13",
    "amount" : 55572.14
  } ]
}
```

## Отчет об остатке кошельков проекта за период

Пероид группировки (tick) - день

### Параметры

* `project_id` - ID проекта, в котором будет производиться поиск. Доступен только суперадминистраторам.
* `from`, `to` - Временной промежуток
* `group_by` - параметр группировки

### Группировка
* `payment_type` - входящий и исходящий потоки
* `service` - по сервисам


```shell
$ curl -uadmin:admin "https://www.synq.ru/mserver2-dev/admin/balance?from=2014-07-11&to=2014-07-13"
```

```json
{
  "meta" : {
    "code" : 200
  },
  "data" : [ {
    "tick" : "2014-07-11",
    "amount" : 55572.14
  }, {
    "tick" : "2014-07-12",
    "amount" : 55572.14
  }, {
    "tick" : "2014-07-13",
    "amount" : 55572.14
  } ]
}

> Пример группировки по типу платежа

```shell
$ curl -uadmin:admin "https://www.synq.ru/mserver2-dev/admin/balance?from=2014-07-11&to=2014-07-11&group_by=payment_type"
```

```json
{
  "meta" : {
    "code" : 200
  },
  "data" : [ {
    "tick" : "2014-07-11",
    "data" : {
      "in": 25572.14,
      "out": 35245.12
      }
  }]
}
```

> Пример группировки по сервису

```shell
$ curl -uadmin:admin "https://www.synq.ru/mserver2-dev/admin/balance?from=2014-07-11&to=2014-07-11&group_by=service"
```

```json
{
  "meta" : {
    "code" : 200
  },
  "data" : [ {
    "tick" : "2014-07-11",
    "data" : [ {
        "service_id": 14,
        "amount": 35245.12
      },{
        "service_id": 16,
        "amount": 3527.10
      } ]
  }]
}
```

## Отчет о количестве платежей проекта за период

### Параметры

* `project_id` - ID проекта, в котором будет производиться поиск. Доступен только суперадминистраторам.
* `from`, `to` - временной промежуток
* `payment_status` - created | processing | completed | declined - статус платежа
* `service_id` - фильтр по сервису или по списку сервисов. Идентификаторы через запятую (11,23,45)
* `group_by` - параметр группировки
* `tick` (30m | 3h | day | month) - выбор разреза при группировке. По умолчанию - день (day).

### Группировка
* `payment_status` - динамика проходимости платежей

> Подсчёт всех платежей за период

```shell
$ curl -uadmin:admin "https://www.synq.ru/mserver2-dev/admin/payments_count?from=2014-07-11&to=2014-07-12"
```

```json
{
  "meta" : {
    "code" : 200
  },
  "data" : [ {
    "tick" : "2014-07-11",
    "count" : 123
  }, {
    "tick" : "2014-07-12",
    "count" : 98
  } ]
}
```

> Пример с группировкой по статусу платежей

```shell
$ curl -uadmin:admin "https://www.synq.ru/mserver2-dev/admin/payments_count?from=2014-07-11&to=2014-07-11&group_by=payment_status&tick=3h"
```

```json
{
  "meta" : {
    "code" : 200
  },
  "data" : [ {
    "tick" : "2014-07-11 00:00:00",
    "count" : {
      "created" : 24,
      "processing" : 37,
      "completed" : 21,
      "declined" : 1
    }
  }, {
    "tick" : "2014-07-11 00:03:00",
    "count" : {
      "created" : 22,
      "processing" : 29,
      "completed" : 31,
      "declined" : 3
    }
  },
  ...
  {
    "tick" : "2014-07-12 00:00:00",
    "count" : {
      "created" : 22,
      "processing" : 29,
      "completed" : 31,
      "declined" : 3
    }
  } ]
}
```


## Получение списка персональных данных

Информация выдаётся постранично. 

### Параметры постраничного запроса

`page` - номер страницы начиная с 0
`size` - размер страницы
`sort` - сортировка по полю, имя поля указывается в camelCase стиле, после запятой может следовать направление сортировки 

```shell
$ curl -uuser:user "https://www.synq.ru/mserver2-dev/admin/persons?page=1&size=2&sort=givenName,desc
```

```json
{
  "meta" : {
    "page" : {
      "total_elements" : 6
    },
    "code" : 200
  },
  "data" : [ {
    "family_name" : "Дергачёв",
    "given_name" : "Андрей",
    "patronymic_name" : "Петрович",
    "passport_series_number" : "45112456789",
    "passport_issued_at" : "2007-06-07",
    "itn" : "526317984689",
    "status" : "data_entered",
    "verified_at" : "2014-10-30T11:11:52.401Z",
    "changed_at" : "2014-09-08T15:47:10.411Z",
    "wallet" : {
      "phone" : "+380631345678"
    }
  }, {
    "family_name" : "Арсеньев",
    "given_name" : "Алексей",
    "patronymic_name" : "Александрович",
    "passport_series_number" : "2202655111",
    "passport_issued_at" : "2012-02-02",
    "itn" : "330500938709",
    "status" : "data_entered",
    "changed_at" : "2014-10-24T15:09:12.019Z",
    "wallet" : {
      "phone" : "+380503839987"
    }
  } ]
}
```

## Изменение статуса персональных данных

### Параметры

* `wallet` - номер телефона в международном формате
* `status` - `data_entered` | `data_verified` статус персональных данных

```shell
$ curl  -H 'Content-type:application/json' -uuser:user -d '{"status": "data_verified"}' "https://www.synq.ru/mserver2-dev/admin/persons/%2B79260000006/update_status" 
```

> Результат содержит `"status": "data_verified", "verified_at": "2014-10-22T10:26:12.035Z"`

```json
{
    "meta": {
        "code": 200
    },
    "data": {
        "family_name": "Иванов",
        "given_name": "Иван",
        "patronymic_name": "Иванович",
        "passport_series_number": "1122334455",
        "passport_issued_at": "2012-12-20",
        "itn": "330500938709",
        "ssn": "11223344595",
        "status": "data_verified",
        "verified_at": "2014-10-22T10:26:12.035Z",
        "changed_at": "2014-10-22T10:26:10.604Z",
        "wallet": {
            "phone": "+380935895452"
        }
    }
}
```

## Удаление кошелька

<aside class="warning">Команда работает только на dev сервере</aside>

```shell
$ curl -uuser:user -X DELETE https://www.synq.ru/mserver2-dev/admin/wallets/+79260000006
```

```json
{
  "meta" : {
    "code" : 200
  }
}
```
