# Административное API


## Аутентификация

На тестовой системе созданы 2 пользователя от имени которых возможен вызов административного API.

| Логин      | Пароль  | Роли пользователя |
| :--------  |:------- |:------------------|
| user       | user    | user              |
| admin      | admin   | admin             |


## Многопроектность

Во всех запросах можно указать относительного какого проекта производится запрос.

* `project_id` - ID проекта, в котором будет производиться поиск. Доступен только суперадминистраторам.


## Постраничная навиция

* `page` - номер (начиная с 0) страницы, которую запрашивает клиент, опционально, по умолчанию 0
* `size` - размер страницы, которую запрашивает клиент, опционально, по умолчанию 35


## Поиск по кошелькам проекта

### Постраничная навиция

См. выше.

### Параметры

* `order_by` - поле для сортировки
* `order_direction` - направление сортировки: asc или desc (case insensitive)

### Поля ответа:

* `phone` - номер телефона, к которому привязан кошелек
* `amount` - остаток на балансе кошелька
* `enabled` - false если кошелек заблокирован, иначе - true
* `active` - true если кошелек активирован через СМС-код
* `role` - роль пользователя (всегда равно user)
* `created_at` - дата регистрации пользователя
* `person.*` - идентификационные данные пользователя
* `statistics.payments.lifetime.turnover` - оборот по кошельку за все время
* `statistics.payments.lifetime.in_turnover` - оборот по транзакциям типа in за все время
* `statistics.payments.lifetime.out_turnover` - оборот по транзакциям типа out за все время
* `statistics.payments.lifetime.p2p_turnover` - оборот по транзакциям типа p2p за все время
* `statistics.payments.lifetime.count` - количество транзакций за все время
* `statistics.payments.lifetime.in_count` - количество транзакций типа in за все время
* `statistics.payments.lifetime.out_count` - количество транзакций типа out за все время
* `statistics.payments.lifetime.p2p_count` - количество транзакций типа out за все время
* `statistics.payments.last_month.turnover` - оборот по кошельку за последний месяц
* `statistics.payments.last_month.in_turnover` - оборот по транзакциям типа in за последний месяц
* `statistics.payments.last_month.out_turnover` - оборот по транзакциям типа out за последний месяц
* `statistics.payments.last_month.p2p_turnover` - оборот по транзакциям типа p2p за последний месяц
* `statistics.payments.last_month.count` - количество транзакций за последний месяц
* `statistics.payments.last_month.in_count` - количество транзакций типа in за последний месяц
* `statistics.payments.last_month.out_count` - количество транзакций типа out за последний месяц
* `statistics.payments.last_month.p2p_count` - количество транзакций типа out за последний месяц
* `statistics.cards.count` - количество привязанных и удаленных карт
* `statistics.cards.active_count` - количество привязанных активных карт

### Поля для сортировки:

* `statistics.payments.lifetime.*` - по любому полю со статистики транзакций за все время
* `statistics.cards.*count` - по любому count полю со статистики количества карт
* `created_at` - по дате регистрации
* `amount` - по остаткам

### Фильтры:

* `ips` - по списку IP адресов разделённых запятой
* `created_before` и `created_after` - по дате регистрации
* `person_given_name`, `person_family_name` и `person_patronymic_name` - по ФИО, поиск полного совпадения или совпадения в начале
* `person_status` - по статусу идентификации
* `person_passport_series_number` - по номеру и серии паспорта
* `phones` - по списку номеров телефонов, разделённых запятыми; поиск полного совпадения или совпадения в начале
* `card_number_first` - по первым 6-ти цифрам номера карты
* `card_number_last` - по последним 4-ем цифрам номера карты
* `card_id` - по ID карты в IPSP
* `amount_from` и `amount_to` - по сумме остатка на кошельке
* `active` - по статусу активации (true|false)
* `disabled` - по статусу блокировки (true|false)

```shell
$ curl -uadmin:admin "https://www.synq.ru/mserver2-dev/admin/wallets?family_name=арсен&active=true&order_by=payment_count&order_direction=desc"
```

```json
{
   "meta":{
      "page":{
         "total_elements":4
      },
      "code":200
   },
   "data":[
      {
         "phone":"+380503839001",
         "amount":8598.17,
         "enabled":true,
         "active":true,
         "role":"user",
         "created_at":"2014-08-20T15:10:25.943Z",
         "person":{
            "family_name":"Арсеньев",
            "given_name":"Алексей",
            "patronymic_name":"Александрович",
            "passport_series_number":"2202655885",
            "passport_issued_at":"2012-02-27",
            "itn":"330500938709",
            "ssn":"11223344595",
            "status":"data_entered"
         },
         "statistics":{
            "payments":{
               "lifetime":{
                  "turnover":8,
                  "in_turnover":8,
                  "out_turnover":8,
                  "p2p_turnover":8,
                  "count":8,
                  "in_count":8,
                  "out_count":8,
                  "p2p_count":8,

               },
               "last_month":{
                  "turnover":8,
                  "in_turnover":8,
                  "out_turnover":8,
                  "p2p_turnover":8,
                  "count":8,
                  "in_count":8,
                  "out_count":8,
                  "p2p_count":8,

               }
            },
            "cards":{
               "count":4,
               "active_count":2
            }
         }
      }
   ]
}
```

## Аналитика по кошелькам

### Параметры

* `group_by` - поле для группировки
* `tick` (day|month) - выбор разреза при группировке (день|месяц). По умолчанию - день.

### Фильтры:
(См. "Поиск по кошелькам")

* `ips` - по списку IP адресов разделённых запятой
* `created_before` и `created_after` - по дате регистрации
* `person_given_name`, `person_family_name` и `person_patronymic_name` - по ФИО, поиск полного совпадения или совпадения в начале
* `person_status` - по статусу идентификации
* `person_passport_series_number` - по номеру и серии паспорта
* `phones` - по списку номеров телефонов, разделённых запятыми; поиск полного совпадения или совпадения в начале
* `card_number_first` - по первым 6-ти цифрам номера карты
* `card_number_last` - по последним 4-ем цифрам номера карты
* `card_id` - по ID карты в IPSP
* `amount_from` и `amount_to` - по сумме остатка на кошельке
* `active` - по статусу активации (true|false)
* `disabled` - по статусу блокировки (true|false)

### Группировки:

* `created_at` - вернет количество новых регистрацией за каждый `tick`

> Количество зарегистрированных кошельков с фильтром

```shell
$ curl -uadmin:admin "https://www.synq.ru/mserver2-dev/admin/wallets/count?active=true"
```

```json
{
   "meta":{
      "code":200
   },
   "data":{
      "count":200
   }
}
```

> Динамика регистраций кошельков с фильтром

```shell
$ curl -uadmin:admin "https://www.synq.ru/mserver2-dev/admin/wallets_count?group_by=created_at&tick=day&created_after=2014-01-01"
```

```json
{
   "meta":{
      "code":200
   },
   "data":[
      {
         "tick":"2014-06-01",
         "data":{
            "count":125477.16
         }
      },
      {
         "tick":"2014-06-02",
         "data":{
            "count":0
         }
      },
      {
         "tick":"2014-06-03",
         "data":{
            "count":0
         }
      }
   ]
}
```


## Загрузка кошелька

### Поля ответа:

* `phone`
* `amount`
* `enabled`
* `active`
* `role`
* `created_at`
* `person.*` - идентификационные данные пользователя
* `cards.*` - данные по картам прикреплённым к кошельку
* `cards.state` - (created | pending | active | failed | deleted | used) - состояние карты
* `cards.3ds` - (unknown | success | failed | none) - вид 3D Secure
* `cards.last_payment_status` - (created | processing | completed | declined) - статус последнего платежа
* `statistics.payments.lifetime.turnover` - оборот по кошельку за все время
* `statistics.payments.lifetime.in_turnover` - оборот по транзакциям типа in за все время
* `statistics.payments.lifetime.out_turnover` - оборот по транзакциям типа out за все время
* `statistics.payments.lifetime.p2p_turnover` - оборот по транзакциям типа p2p за все время
* `statistics.payments.lifetime.count` - количество транзакций за все время
* `statistics.payments.lifetime.in_count` - количество транзакций типа in за все время
* `statistics.payments.lifetime.out_count` - количество транзакций типа out за все время
* `statistics.payments.lifetime.p2p_count` - количество транзакций типа out за все время
* `statistics.payments.last_month.turnover` - оборот по кошельку за последний месяц
* `statistics.payments.last_month.in_turnover` - оборот по транзакциям типа in за последний месяц
* `statistics.payments.last_month.out_turnover` - оборот по транзакциям типа out за последний месяц
* `statistics.payments.last_month.p2p_turnover` - оборот по транзакциям типа p2p за последний месяц
* `statistics.payments.last_month.count` - количество транзакций за последний месяц
* `statistics.payments.last_month.in_count` - количество транзакций типа in за последний месяц
* `statistics.payments.last_month.out_count` - количество транзакций типа out за последний месяц
* `statistics.payments.last_month.p2p_count` - количество транзакций типа out за последний месяц
* `statistics.cards.count` - количество привязанных и удаленных карт
* `statistics.cards.active_count` - количество привязанных активных карт

```shell
$ curl -uuser:user "https://www.synq.ru/mserver2-dev/admin/wallets/%2B79260000006"
```

```json
{
   "meta":{
      "code":200
   },
   "data":{
      "phone":"+79260000006",
      "amount":65572.14,
      "enabled":true,
      "active":true,
      "role":"user",
      "created_at":"2014-06-01T12:43:52.876Z",
      "person":{
         "family_name":"Арсеньев",
         "given_name":"Алексей",
         "patronymic_name":"Александрович",
         "passport_series_number":"2202655885",
         "passport_issued_at":"2012-02-27",
         "itn":"330500938709",
         "ssn":"11223344595",
         "status":"data_entered"
      },
      "cards":[
         {
            "card_id":14,
            "state":"used",
            "title":"541715******2399",
            "type":"MasterCard",
            "3ds":"none",
            "lifetime_turnover":245435.56,
            "card_holder_name":"Ivanov Ivan",
            "last_payment_status":"completed"
         }
      ],
      "limits":{
         "amount": {
            "in": {
               "limit": 15000,
               "available": 15000
            },
            "out": {
               "limit": 15000,
               "available": 15000
            },
            "p2p": {
               "limit": 15000,
               "available": 15000
            },
            "wallet": {
               "limit": 15000,
               "available": 15000
            },
         },
         "turnover": {
             "monthly": {
                "in": {
                   "limit": 15000,
                   "available": 15000
                },
                "out": {
                   "limit": 15000,
                   "available": 15000
                },
                "p2p": {
                   "limit": 15000,
                   "available": 15000
                },
             },
         },
         "cards": {
            "active": {
               "limit": 10,
               "available": 8,
            }
         }
      },
      "statistics":{
         "payments":{
            "lifetime":{
               "turnover":8,
               "in_turnover":8,
               "out_turnover":8,
               "p2p_turnover":8,
               "count":8,
               "in_count":8,
               "out_count":8,
               "p2p_count":8,

            },
            "last_month":{
               "turnover":8,
               "in_turnover":8,
               "out_turnover":8,
               "p2p_turnover":8,
               "count":8,
               "in_count":8,
               "out_count":8,
               "p2p_count":8,

            }
         },
         "cards":{
            "count":4,
            "active_count":2
         }
      }
   }
}
```

## Загрузка IP адресов кошелька

```shell
$ curl -uuser:user "https://www.synq.ru/mserver2-dev/admin/wallets/%2B12345657367/ip"
```

```json
{
  "meta" : {
    "code" : 200,
    "page" : {
      "total_elements" : 21
    }
  },
  "data" : [ "127.0.0.1", "::1"]
}
```

## Получение кода активации кошелька

```shell
$ curl -uuser:user "https://www.synq.ru/mserver2-dev/admin/wallets/%2B12345657367/security_code"
```

```json
{
   "meta":{
      "code":200
   },
   "data":{
      "security_code":"2899066"
   }
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
   "meta":{
      "code":200
   },
   "data":{
      "phone":"+12345657367",
      "amount":10000,
      "reset_password":false,
      "lock_message":"Заблокироваан до выяснения.",
      "verified":false,
      "enabled":false,
      "active":true,
      "lock_reason":"Клиент - кардер.",
      "locked_at":"2014-08-14T16:46:42.122Z"
   }
}
```


## Разблокировка кошелька

```shell
$ curl -uuser:user -X POST "https://www.synq.ru/mserver2-dev/admin/wallets/%2B12345657367/enable"
```

```json
{
   "meta":{
      "code":200
   },
   "data":{
      "phone":"+12345657367",
      "amount":10000,
      "reset_password":false,
      "verified":false,
      "enabled":true,
      "active":true
   }
}
```


## Поиск по платежам

### Параметры (опциональные)

* `wallet` - телефон кошелька, чьи платежи мы хотим видеть
* `type` - тип платежа
* `status`- статус платежа
* `service_name` - полное или частичное имя сервиса
* `service_ids` - список ID сервиса (как альтернатива service_name), ID сервисов перечисляются через запятую, например: 3,19,293
* `amount_from` и `amount_to` - границы диапазона сумм платежей, формат UTC
* `date_from` и `date_to` - границы диапазона дат создания платежей
* `page` - номер (начиная с 0) страницы, которую запрашивает клиент, по умолчанию 0
* `size` - размер страницы, которую запрашивает клиент, по умолчанию 20
* `order_by` - поле для сортировки
* `order_direction` - направление сортировки
* `client_ip` - IP адрес плательщика (или сервиса с которого поступило распоряжение на списание)
* `inbound_payment_id` - идентификатор платежа из IPSP
* `inbound_payment_status` - идентификатор платежа из IPSP
* `inbound_payment_amount_from` и `inbound_payment_amount_to` - границы диапазона сумм платежей IPSP
* `inbound_payment_date_from` и `inbound_payment_date_to` - границы диапазона дат создания платежей IPSP
* `inbound_payment_card_number_first` - поиск по первым 6 цифрам номера карты
* `inbound_payment_card_number_last` - поиск по последним 4 цифрам номера карты
* `inbound_payment_card_number_last` - поиск по последним 4 цифрам номера карты
* `inbound_payment_recurring` - фильтр по рекурентным платежам (true/false)
* `inbound_payment_card_type` - поиск по вендору карт (MASTER_CARD/VISA/..?)
* `inbound_payment_3ds` - поиск по 3ds статусу карты: unknown (ECI/SLI не получен ни на одном из шагов), attempted (06), skipped (07), successful (05)
* `inbound_payment_user_ip` - поиск по IP адресу плательщика

```shell
$ curl -uuser:user "https://www.synq.ru/mserver2-dev/admin/payments?service_name=mts&type=out&status=created&amount_from=0&amount_to=100000&date_from=2014-01-01T12:10:15.525Z&date_to=2014-12-01T00:00:00.00Z&order_by=amount&order_direction=desc&size=1"
```

```json
{
   "meta":{
      "page":{
         "total_elements":14
      },
      "code":200
   },
   "data":[
      {
         "id":1401089244704,
         "client_payment_id":"409c1e06-5faa-11e4-a61c-b88d12284ddc",
         "amount":12000,
         "total":12070,
         "created_at":"2014-10-29T20:29:16.495Z",
         "status":"created",
         "type":"out",
         "service":{
            "id":15,
            "name":"MTS Украина"
         },
         "parameters":[
            {
               "code":"phoneNumber",
               "name":"№ телефона (9-10 цифр)",
               "value":"0509244512"
            }
         ],
         "outbound":{
            "id":35,
            "code":"tpr_out",
            "name":"ООО ТПР (провайдер)"
         },
         "wallet":{
            "phone":"+79270000001",
            "amount":8496.32,
            "verified":false,
            "ip":"37.110.42.197"
         }
      }
   ]
}
```

> Пример фильтра по кошельку и IP

```shell
$ curl -uuser:user "https://www.synq.ru/mserver2-dev/admin/payments?wallet=%2B380935895452&client_ip=127.0.0.1&size=1"
```

```json
{
   "meta":{
      "page":{
         "total_elements":2
      },
      "code":200
   },
   "data":[
      {
         "id":1401089245266,
         "client_payment_id":"96c280f4-8e2b-40a1-b250-806bb4a4b9f1",
         "amount":10000,
         "total":10000,
         "created_at":"2014-11-04T13:02:09.409Z",
         "processed_at":"2014-11-04T13:02:13.619Z",
         "status":"completed",
         "type":"p2p",
         "wallet":{
            "phone":"+380935895452",
            "amount":0,
            "name":"Иван Иванов",
            "verified":true,
            "ip":"127.0.0.1"
         },
         "destination":{
            "phone":"+79555555555"
         },
         "direction":"out",
         "client_ip":"127.0.0.1"
      }
   ]
}
```

> Пример фильтра по inbound_payment_id (id платежа в IPSP)

```shell
$ curl -uuser:user "https://www.synq.ru/mserver2-dev/admin/payments?inbound_payment_id=6143708"
```

```json
{
   "meta":{
      "code":200,
      "page":{
         "total_elements":1
      }
   },
   "data":[
      {
         "id":1401089245752,
         "client_payment_id":"6ea16c52-669e-11e4-86b1-3c07542cf2f2",
         "amount":42,
         "total":42,
         "created_at":"2014-11-07T16:52:17.990Z",
         "processed_at":"2014-11-07T16:52:21.791Z",
         "status":"completed",
         "type":"in",
         "inbound":{
            "id":62,
            "code":"ipsp_in",
            "name":"ООО ИПСП (агент)",
            "payment":{
               "amount":4200,
               "id":1401089245752,
               "type":"SALE",
               "date":"2014-11-07T16:52:19.804Z",
               "product_id":1721,
               "currency":"RUB",
               "card_holder_name":"TESTER TESTEROV",
               "exp_year":2014,
               "exp_month":11,
               "remote_ip":"81.95.134.13",
               "user_ip":"81.95.134.13",
               "card_number_mask":"541715******2399",
               "card_type":"MASTER_CARD",
               "recurring":false,
               "steps":[
                  {
                     "date":"2014-11-07T16:52:19.867Z",
                     "status":"PASSED_0",
                     "type":"ANTIFRAUD"
                  },
                  {
                     "eci":"06",
                     "date":"2014-11-07T16:52:21.571Z",
                     "status":"PASSED_0",
                     "type":"BANK",
                     "auth_id_response":"411421",
                     "date_local_trans":"2014-11-07T13:52:21.000Z",
                     "response_code":"APPROVED_00"
                  },
                  {
                     "date":"2014-11-07T16:52:19.843Z",
                     "status":"PASSED_0",
                     "type":"PAYMENT_INPUT"
                  }
               ]
            }
         },
         "card":{
            "state":"used",
            "title":"541715******2399",
            "type":"MasterCard",
            "bin":{
               "country":"Ukraine",
               "bank":"JCB PrivatBank",
               "type":"debit"
            }
         },
         "wallet":{
            "phone":"+79270000001",
            "amount":7462.54,
            "level":"anonymous",
            "name":"Пиэчпий Мбанков",
            "verified":true,
            "person_status":"data_verified",
            "enabled":true,
            "active":true,
            "role":"user",
            "created_at":"2014-10-29T16:33:14.045Z"
         },
         "client_ip":"81.95.134.13"
      }
   ]
}
```


## Отчет об остатке кошельков проекта за период

### Параметры

* `date_from`, `date_to` - Временной промежуток

```shell
$ curl -uadmin:admin "https://www.synq.ru/mserver2-dev/admin/wallets/balance?from=2014-07-11&to=2014-07-13"
```

```json
{
   "meta":{
      "code":200
   },
   "data":[
      {
         "tick":"2014-07-11",
         "data":{
            "amount":55572.14
         }
      },
      {
         "tick":"2014-07-12",
         "data":{
            "amount":55572.14
         }
      },
      {
         "tick":"2014-07-13",
         "data":{
            "amount":55572.14
         }
      }
   ]
}
```


## Отчет о количестве платежей проекта за период

### Параметры

* `date_from`, `date_to` - (фильтр) временной промежуток, по-умолчанию 1 месяц с текущего момента
* `status` - (фильтр) created | processing | completed | declined - статус платежа
* `service_ids` - (фильтр) сервис или список идентификаторов сервисов через запятую и/или флаг p2p (11,23,45,p2p)
* `group_by` - параметр группировки
* `wallet` - фильтр по номеру кошелька (смотреть данные в разрезе кошелька)
* `type` - (in | out | inout | p2p) фильтр по типу транзакции
* `tick` (30m | 3h | day | month) - выбор разреза при группировке. По умолчанию - день (day).

### Группировки
* `status` - для динамики проходимости платежей
* `service_ids` - для распределения платежей по сервисам, включая P2P как отдельный тип
* `service_ids,status` - по сервисам и статусам
* `provider_types` - по типу провайдеров, через которые проходили транзакции

> Подсчёт всех платежей за период

```shell
$ curl -uadmin:admin "https://www.synq.ru/mserver2-dev/admin/payments/count?date_from=2014-07-11&date_to=2014-07-12"
```

```json
{
   "meta":{
      "code":200
   },
   "data":[
      {
         "tick":"2014-07-11",
         "data": {
             "count":123
         }
      },
      {
         "tick":"2014-07-12",
         "data": {
             "count":98
         }
      }
   ]
}
```

> Пример с группировкой по статусу платежей

```shell
$ curl -uadmin:admin "https://www.synq.ru/mserver2-dev/admin/payments/count?date_from=2014-07-11&date_to=2014-07-11&group_by=status&tick=3h"
```

```json
{
   "meta":{
      "code":200
   },
   "data":[
      {
         "tick":"2014-07-11T00:00:00",
         "data":{
            "count":83,
            "created":24,
            "processing":37,
            "completed":21,
            "declined":1
         }
      },
      {
         "tick":"2014-07-11T00:03:00",
         "data":{
            "count":83,
            "created":24,
            "processing":37,
            "completed":21,
            "declined":1
         }
      },
      ...
      {
         "tick":"2014-07-12T00:00:00",
         "data":{
            "count":83,
            "created":24,
            "processing":37,
            "completed":21,
            "declined":1
         }
      }
   ]
}
```

> Пример группировки по типу поставщика

```shell
$ curl -uadmin:admin "https://www.synq.ru/mserver2-dev/admin/payments/count?date_from=2014-07-11&date_to=2014-07-11&group_by=provider_types&tick=3h"
```

```json
{
   "meta":{
      "code":200
   },
   "data":[
      {
         "tick":"2014-07-12T00:00:00",
         "data":{
            "count":83,
            "providers":[
               {
                  "id" : 1,
                  "code" : "tpr_out",
                  "name" : "Кредит Пилот",
                  "type" : "outbound", 
                  "count": 50
               },
               {
                  "id" : 4,
                  "code" : "ipsp_in",
                  "name" : "ООО ИПСП (агент)",
                  "type" : "inbound",
                  "count": 50
                }
            ]
         }
      }
      ...
   ]
}
```

> Пример группировки по сервису

```shell
$ curl -uadmin:admin "https://www.synq.ru/mserver2-dev/admin/payments/count?date_from=2014-07-11&date_to=2014-07-11&group_by=service_ids&tick=3h"
```

```json
{
   "meta":{
      "code":200
   },
   "data":[
      {
         "tick":"2014-07-12T00:00:00",
         "data":{
            "count":83,
            "services":[
               {
                  "type" : "out",
                  "id" : 834,
                  "count": 50
               },
               {
                  "type" : "out",
                  "id" : 4,
                  "count": 30
                },
                {
                  "type" : "p2p",
                  "count": 20
                }
            ]
         }
      }
      ...
   ]
}
```

> Пример группировки по сервису и статусу

```shell
$ curl -uadmin:admin "https://www.synq.ru/mserver2-dev/admin/payments/count?tick=month&service_ids=1691&group_by=service_ids,status"
```

```json
{
  "meta" : {
    "code" : 200,
    "page" : {
      "total_elements" : 2
    }
  },
  "data" : [ {
    "data" : {
      "count" : 66,
      "services" : [ {
        "data" : {
          "declined" : 2,
          "created" : 51,
          "count" : 61,
          "completed" : 8
        },
        "id" : 1691,
        "type" : "out"
      }, {
        "data" : {
          "count" : 5,
          "completed" : 5
        },
        "id" : 1691,
        "type" : "inout"
      } ]
    },
    "tick" : "2015-02-28T22:00:00.000+0000"
  }, {
    "data" : {
      "count" : 15,
      "services" : [ {
        "data" : {
          "created" : 7,
          "count" : 14,
          "completed" : 7
        },
        "id" : 1691,
        "type" : "out"
      }, {
        "data" : {
          "declined" : 1,
          "count" : 1
        },
        "id" : 1691,
        "type" : "inout"
      } ]
    },
    "tick" : "2015-03-31T21:00:00.000+0000"
  } ]
}
```

## Отчет о обороте платежей проекта за период

### Параметры

* `date_from`, `date_to` - (фильтр) временной промежуток, по-умолчанию 1 месяц с текущего момента
* `amount_from`, `amount_to` - (фильтр) по сумме платежа
* `status` - (фильтр) created | processing | completed | declined - статус платежа
* `service_ids` - (фильтр) сервис или сервисок идентификаторов сервисов через запятую и/или флаг p2p (11,23,45,p2p) 
* `group_by` - параметр группировки
* `wallet` - фильтр по номеру кошелька (смотреть данные в разрезе кошелька)
* `type` - (in | out | inout | p2p) фильтр по типу транзакции
* `tick` (30m | 3h | day | month) - выбор разреза при группировке. По умолчанию - день (day).

### Группировки
* `status` - для динамики проходимости платежей
* `service_ids` - для распределения платежей по сервисам, включая P2P как отдельный тип
* `service_ids,status` - по сервисам и статусам
* `provider_types` - по типу провайдеров, через которые проходили транзакции

> Оборот всех платежей за период
* `date_from` и `date_to` - границы диапазона дат создания платежей
* `ipsp_payment_id` - идентификатор платежа из IPSP
* `page` - номер (начиная с 0) страницы, которую запрашивает клиент, по умолчанию 0
* `size` - размер страницы, которую запрашивает клиент, по умолчанию 20
* `sort` - поле сортировки, через запятую может следовать направление

```shell
$ curl -uadmin:admin "https://www.synq.ru/mserver2-dev/admin/payments/turnover?date_from=2014-07-11&date_to=2014-07-12"
```

```json
{
   "meta":{
      "code":200
   },
   "data":[
      {
         "tick":"2014-07-11",
         "data": {
             "amount":1233424
         }
      },
      {
         "tick":"2014-07-12",
         "data": {
             "amount":98234342
         }
      }
   ]
}
```

> Пример с группировкой по статусу платежей

```shell
$ curl -uadmin:admin "https://www.synq.ru/mserver2-dev/admin/payments/turnover?date_from=2014-07-11&date_to=2014-07-11&group_by=status&tick=3h"
```

```json
{
   "meta":{
      "code":200
   },
   "data":[
      {
         "tick":"2014-07-11T00:00:00",
         "data":{
            "amount":83324324324,
            "created":2434234,
            "processing":3723434,
            "completed":2123423,
            "declined":12
         }
      },
      ...
   ]
}
```

> Пример группировки по типу поставщика

```shell
$ curl -uadmin:admin "https://www.synq.ru/mserver2-dev/admin/payments/turnover?date_from=2014-07-11&date_to=2014-07-11&group_by=provider_types&tick=3h"
```

```json
{
   "meta":{
      "code":200
   },
   "data":[
      {
         "tick":"2014-07-12T00:00:00",
         "data":{
            "amount":83,
            "providers":[
               {
                  "id" : 1,
                  "code" : "tpr_out",
                  "name" : "Кредит Пилот",
                  "type" : "outbound", 
                  "amount": 5043
               },
               {
                  "id" : 4,
                  "code" : "ipsp_in",
                  "name" : "ООО ИПСП (агент)",
                  "type" : "inbound",
                  "amount": 1293
                }
            ]
         }
      }
      ...
   ]
}
```

> Пример группировки по сервису

```shell
$ curl -uadmin:admin "https://www.synq.ru/mserver2-dev/admin/payments/turnover?date_from=2014-07-11&date_to=2014-07-11&group_by=service_ids&tick=3h"
```

```json
{
   "meta":{
      "code":200
   },
   "data":[
      {
         "tick":"2014-07-12T00:00:00",
         "data":{
            "amount":83243,
            "services":[
               {
                  "type" : "out",
                  "id" : 834,
                  "amount": 50234
               },
               {
                  "type" : "out",
                  "id" : 4,
                  "amount": 3012
               },
               {
                  "type" : "p2p",
                  "amount": 2033
               }
            ]
         }
      }
      ...
   ]
}
```

> Пример группировки по сервису и статусу

```shell
$ curl -uadmin:admin "https://www.synq.ru/mserver2-dev/admin/payments/turnover?tick=month&service_ids=1691&group_by=service_ids,status"
```

```json
{
  "meta" : {
    "code" : 200,
    "page" : {
      "total_elements" : 2
    }
  },
  "data" : [ {
    "data" : {
      "amount" : 154,
      "services" : [ {
        "data" : {
          "amount" : 144,
          "declined" : 20,
          "created" : 114,
          "completed" : 10
        },
        "id" : 1691,
        "type" : "out"
      }, {
        "data" : {
          "amount" : 10,
          "completed" : 10
        },
        "id" : 1691,
        "type" : "inout"
      } ]
    },
    "tick" : "2015-02-28T22:00:00.000+0000"
  }, {
    "data" : {
      "amount" : 114,
      "services" : [ {
        "data" : {
          "amount" : 14,
          "created" : 7,
          "completed" : 7
        },
        "id" : 1691,
        "type" : "out"
      }, {
        "data" : {
          "amount" : 100,
          "declined" : 100
        },
        "id" : 1691,
        "type" : "inout"
      } ]
    },
    "tick" : "2015-03-31T21:00:00.000+0000"
  } ]
}
```

## Получение списка персональных данных

Информация выдаётся постранично.

### Параметры постраничного запроса

`page` - номер страницы начиная с 0
`size` - размер страницы
`order_by` - сортировка по полю, имя поля указывается в snake_case стиле
`order_direction` - направление сортировки
`status` - фильтр по статусу сообщений. Например, для получения списка кошельков ожидающих идентификации

```shell
$ curl -uuser:user "https://www.synq.ru/mserver2-dev/admin/persons?page=1&size=2&order_by=givenName&order_direction=desc&status=data_entered
```

```json
{
   "meta":{
      "page":{
         "total_elements":6
      },
      "code":200
   },
   "data":[
      {
         "family_name":"Дергачёв",
         "given_name":"Андрей",
         "patronymic_name":"Петрович",
         "passport_series_number":"45112456789",
         "passport_issued_at":"2007-06-07",
         "itn":"526317984689",
         "status":"data_entered",
         "verified_at":"2014-10-30T11:11:52.401Z",
         "changed_at":"2014-09-08T15:47:10.411Z",
         "wallet":{
            "phone":"+380631345678"
         }
      },
      {
         "family_name":"Арсеньев",
         "given_name":"Алексей",
         "patronymic_name":"Александрович",
         "passport_series_number":"2202655111",
         "passport_issued_at":"2012-02-02",
         "itn":"330500938709",
         "status":"data_entered",
         "changed_at":"2014-10-24T15:09:12.019Z",
         "wallet":{
            "phone":"+380503839987"
         }
      }
   ]
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
   "meta":{
      "code":200
   },
   "data":{
      "family_name":"Иванов",
      "given_name":"Иван",
      "patronymic_name":"Иванович",
      "passport_series_number":"1122334455",
      "passport_issued_at":"2012-12-20",
      "itn":"330500938709",
      "ssn":"11223344595",
      "status":"data_verified",
      "verified_at":"2014-10-22T10:26:12.035Z",
      "changed_at":"2014-10-22T10:26:10.604Z",
      "wallet":{
         "phone":"+380935895452"
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
   "meta":{
      "code":200
   }
}
```
