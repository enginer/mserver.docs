# Транзитный платеж

Транзитный платеж соединяет в одном действии зачисление денег на кошелек через поставщика ввода средств (в настоящее время ИПСП) и списание денег со счета кошелька в пользу поставщика вывода средств (в настоящее время КредитПилот).

По аналогии с пополнением кошелька возможны следующие варианты транзитного платежа:

* однократное списание с карты
* однократное списание с карты с сохранением данных карты для последующего использования ("привязка карты")
* списание средств с сохраненной ранее карты ("рекарринг")

### Параметры

* `type` = inout - тип платежа
* `client_payment_id` - клиентский идентификатор платежа (опциональный)
* `amount` -  сумма к зачислению
* `store_card` - `true` | `false` - сохранить карту, чтобы использовать ее для платежей в дальнейшем (опциональный)
* `card` - идентификатор сохраненной карты с которой будут списаны деньги (опциональный)
* `service` - назначение платежа, идентификатор Сервиса
* `parameters` - параметры платежного запроса в соответствии с /services/{service}

## Однократное списание с карты

> Создаем платеж:

```shell
$ curl -u+79261111111:password -H 'Content-type:application/json' 
-d '{"type": "inout", "amount": 100, "service": 834, "parameters": {"phoneNumber": "9267101283"}}' 
https://www.synq.ru/mserver2-dev/v1/payments
```

```json
{
  "meta" : {
    "code" : 200,
    "urgent_data" : {
      "amount" : 10000
    },
    "next_action" : "pay"
  },
  "data" : {
    "id" : 18357,
    "client_payment_id" : "9de7de50-cb15-40f0-a953-c77df91803c9",
    "amount" : 100,
    "total" : 100.00,
    "created_at" : "2015-02-02T12:23:57.548Z",
    "status" : "created",
    "type" : "inout",
    "service" : {
      "id" : 834,
      "name" : "Мегафон"
    },
    "parameters" : [ {
      "code" : "phoneNumber",
      "name" : "№ Телефона",
      "value" : "9267101283"
    } ],
    "inbound" : {
      "id" : 4,
      "code" : "ipsp_in",
      "name" : "ООО ИПСП (агент)"
    },
    "outbound" : {
      "id" : 1,
      "code" : "tpr_out",
      "name" : "Кредит Пилот"
    },
    "card" : {
      "state" : "created"
    },
    "wallet" : {
      "phone" : "+79261111111"
    }
  }
}
```

> Платим:

```shell
$ curl -u+79261111111:password -H 'Content-type:application/json' -X POST 
https://www.synq.ru/mserver2-dev/v1/payments/18357/pay
```

```json
{
  "meta" : {
    "code" : 200,
    "urgent_data" : {
      "amount" : 10000
    },
    "next_action" : "get"
  },
  "data" : {
    "id" : 18357,
    "client_payment_id" : "9de7de50-cb15-40f0-a953-c77df91803c9",
    "amount" : 100,
    "total" : 100,
    "created_at" : "2015-02-02T12:23:57.548Z",
    "status" : "processing",
    "type" : "inout",
    "service" : {
      "id" : 834,
      "name" : "Мегафон"
    },
    "parameters" : [ {
      "code" : "phoneNumber",
      "name" : "№ Телефона",
      "value" : "9267101283"
    } ],
    "inbound" : {
      "id" : 4,
      "code" : "ipsp_in",
      "name" : "ООО ИПСП (агент)"
    },
    "outbound" : {
      "id" : 1,
      "code" : "tpr_out",
      "name" : "Кредит Пилот"
    },
    "card" : {
      "state" : "pending",
      "payment_page_url" : "https://test1.ipsp.com/frontend/endpoint?product_id=1721&desc=BestWallet&payment_type=S&amount=100.00&currency=RUB&cf=18357&locale=ru&hash=1a6752f0f13df41aaa7dd24419ab04b1b1743b15"
    },
    "wallet" : {
      "phone" : "+79261111111"
    }
  }
}
```

> далее следует перенаправить пользователя на платежную страницу по ссылке из поля `card.payment_page_url`.

> Проверяем статус платежа:

```shell
$ curl -u+79261111111:password -H 'Content-type:application/json' 
https://www.synq.ru/mserver2-dev/v1/payments/18357
```

```json
{
  "meta" : {
    "code" : 200,
    "urgent_data" : {
      "amount" : 10000
    },
    "next_action" : "get"
  },
  "data" : {
    "id" : 18357,
    "client_payment_id" : "9de7de50-cb15-40f0-a953-c77df91803c9",
    "amount" : 100,
    "total" : 100,
    "created_at" : "2015-02-02T12:23:57.548Z",
    "processed_at" : "2015-02-02T12:27:13.279Z",
    "status" : "completed",
    "type" : "inout",
    "service" : {
      "id" : 834,
      "name" : "Мегафон"
    },
    "parameters" : [ {
      "code" : "phoneNumber",
      "name" : "№ Телефона",
      "value" : "9267101283"
    } ],
    "inbound" : {
      "id" : 4,
      "code" : "ipsp_in",
      "name" : "ООО ИПСП (агент)"
    },
    "outbound" : {
      "id" : 1,
      "code" : "tpr_out",
      "name" : "Кредит Пилот"
    },
    "card" : {
      "state" : "used",
      "title" : "541715******7260",
      "type" : "MasterCard"
    },
    "wallet" : {
      "phone" : "+79261111111"
    },
    "remote_check" : "1422880082862"
  }
}
```

> Платеж успешен.

## С сохранением данных карты

Сценарий ничем не отличается от предыдущего, кроме передачи флага "store_card": true при создании платежа. 
Идентификатор карты, возвращенный в `data.card.id` можно впоследствии использовать для пополнения счета кошелька 
или выполнения транзитных платежей с сохраненной карты.

> Создаем платеж:

```shell
$ curl -u+79261111111:password -H 'Content-type:application/json' 
-d '{"type": "inout", "amount": 100, "store_card": true, "service": 834, "parameters": {"phoneNumber": "9267101283"}}' 
https://www.synq.ru/mserver2-dev/v1/payments
```

```json
{
  "meta" : {
    "code" : 200,
    "urgent_data" : {
      "amount" : 10000
    },
    "next_action" : "pay"
  },
  "data" : {
    "id" : 18387,
    "client_payment_id" : "f80143c5-29aa-4514-8aee-94acc3c3a026",
    "amount" : 100,
    "total" : 100.00,
    "created_at" : "2015-02-02T13:06:35.191Z",
    "status" : "created",
    "type" : "inout",
    "service" : {
      "id" : 834,
      "name" : "Мегафон"
    },
    "parameters" : [ {
      "code" : "phoneNumber",
      "name" : "№ Телефона",
      "value" : "9267101283"
    } ],
    "inbound" : {
      "id" : 4,
      "code" : "ipsp_in",
      "name" : "ООО ИПСП (агент)"
    },
    "outbound" : {
      "id" : 1,
      "code" : "tpr_out",
      "name" : "Кредит Пилот"
    },
    "card" : {
      "id" : 7221,
      "state" : "created"
    },
    "wallet" : {
      "phone" : "+79261111111"
    }
  }
}
```

> Платим:

```shell
$ curl -u+79261111111:password -H 'Content-type:application/json' -X POST 
https://www.synq.ru/mserver2-dev/v1/payments/18387/pay
```

```json
{
  "meta" : {
    "code" : 200,
    "urgent_data" : {
      "amount" : 10000
    },
    "next_action" : "get"
  },
  "data" : {
    "id" : 18387,
    "client_payment_id" : "f80143c5-29aa-4514-8aee-94acc3c3a026",
    "amount" : 100,
    "total" : 100,
    "created_at" : "2015-02-02T13:06:35.191Z",
    "status" : "processing",
    "type" : "inout",
    "service" : {
      "id" : 834,
      "name" : "Мегафон"
    },
    "parameters" : [ {
      "code" : "phoneNumber",
      "name" : "№ Телефона",
      "value" : "9267101283"
    } ],
    "inbound" : {
      "id" : 4,
      "code" : "ipsp_in",
      "name" : "ООО ИПСП (агент)"
    },
    "outbound" : {
      "id" : 1,
      "code" : "tpr_out",
      "name" : "Кредит Пилот"
    },
    "card" : {
      "id" : 7221,
      "state" : "pending",
      "payment_page_url" : "https://test1.ipsp.com/frontend/endpoint?product_id=1721&desc=BestWallet&payment_type=S&amount=100.00&currency=RUB&cf=18386&locale=ru&biller_client_id=82c9273ea7e0455d96efd0864f3042a6&perspayee_expiry=0150&recur_freq=1&hash=9621bc5f10d1430b2143fcbdb3c6a4be9b545aba"
    },
    "wallet" : {
      "phone" : "+79261111111"
    }
  }
}
```

> далее следует перенаправить пользователя на платежную страницу по ссылке из поля `card.payment_page_url`.

> Проверяем статус платежа:

```shell
$ curl -u+79261111111:password https://www.synq.ru/mserver2-dev/v1/payments/18387
```

```json
{
  "meta" : {
    "code" : 200,
    "urgent_data" : {
      "amount" : 10000
    },
    "next_action" : "get"
  },
  "data" : {
    "id" : 18387,
    "client_payment_id" : "1fa097f7-63a3-4778-aa3c-c62fc5e85045",
    "amount" : 100,
    "total" : 100,
    "created_at" : "2015-02-02T13:24:12.185Z",
    "processed_at" : "2015-02-02T13:24:57.711Z",
    "status" : "completed",
    "type" : "inout",
    "service" : {
      "id" : 834,
      "name" : "Мегафон"
    },
    "parameters" : [ {
      "code" : "phoneNumber",
      "name" : "№ Телефона",
      "value" : "9267101283"
    } ],
    "inbound" : {
      "id" : 4,
      "code" : "ipsp_in",
      "name" : "ООО ИПСП (агент)"
    },
    "outbound" : {
      "id" : 1,
      "code" : "tpr_out",
      "name" : "Кредит Пилот"
    },
    "card" : {
      "id" : 7222,
      "state" : "active",
      "title" : "541715******6825",
      "type" : "MasterCard"
    },
    "wallet" : {
      "phone" : "+79261111111"
    },
    "remote_check" : "1422883547606"
  }
}
```

> Платеж успешен. Карта с ID 7222 успешно сохранена (имеет статус `active`).

## C сохраненной карты

Транзитный платеж с сохраненной карты отличается от платежа с однократным списанием только указанием идентификатора карты в поле `card` при создании платежа.

> Создаем платеж используя карту с ID 7222, сохраненную в примере выше:

```shell
$ curl -u+79261111111:password -H 'Content-type:application/json' 
-d '{"type": "inout", "amount": 100, "card": 7222, "service": 834, "parameters": {"phoneNumber": "9267101283"}}' 
https://www.synq.ru/mserver2-dev/v1/payments
```

```json
{
  "meta" : {
    "code" : 200,
    "urgent_data" : {
      "amount" : 10000
    },
    "next_action" : "pay"
  },
  "data" : {
    "id" : 18388,
    "client_payment_id" : "8ff2a629-e397-42f5-9610-c6dbd12627d0",
    "amount" : 100,
    "total" : 100.00,
    "created_at" : "2015-02-02T13:31:01.643Z",
    "status" : "created",
    "type" : "inout",
    "service" : {
      "id" : 834,
      "name" : "Мегафон"
    },
    "parameters" : [ {
      "code" : "phoneNumber",
      "name" : "№ Телефона",
      "value" : "9267101283"
    } ],
    "inbound" : {
      "id" : 4,
      "code" : "ipsp_in",
      "name" : "ООО ИПСП (агент)"
    },
    "outbound" : {
      "id" : 1,
      "code" : "tpr_out",
      "name" : "Кредит Пилот"
    },
    "card" : {
      "id" : 7222,
      "state" : "active",
      "title" : "541715******6825",
      "type" : "MasterCard"
    },
    "wallet" : {
      "phone" : "+79261111111"
    }
  }
}
```

> Платим:

```shell
$ curl -u+79261111111:password -H 'Content-type:application/json' -X POST 
https://www.synq.ru/mserver2-dev/v1/payments/18388/pay
```

```json
{
  "meta" : {
    "code" : 200,
    "urgent_data" : {
      "amount" : 10000
    },
    "next_action" : "get"
  },
  "data" : {
    "id" : 18388,
    "client_payment_id" : "8ff2a629-e397-42f5-9610-c6dbd12627d0",
    "amount" : 100,
    "total" : 100,
    "created_at" : "2015-02-02T13:31:01.643Z",
    "status" : "processing",
    "type" : "inout",
    "service" : {
      "id" : 834,
      "name" : "Мегафон"
    },
    "parameters" : [ {
      "code" : "phoneNumber",
      "name" : "№ Телефона",
      "value" : "9267101283"
    } ],
    "inbound" : {
      "id" : 4,
      "code" : "ipsp_in",
      "name" : "ООО ИПСП (агент)"
    },
    "outbound" : {
      "id" : 1,
      "code" : "tpr_out",
      "name" : "Кредит Пилот"
    },
    "card" : {
      "id" : 7222,
      "state" : "active",
      "title" : "541715******6825",
      "type" : "MasterCard"
    },
    "wallet" : {
      "phone" : "+79261111111"
    }
  }
}
```

> Проверяем статус платежа:

```shell
$ curl -u+79261111111:password https://www.synq.ru/mserver2-dev/v1/payments/18388
```

```json
{
  "meta" : {
    "code" : 200,
    "urgent_data" : {
      "amount" : 10000
    },
    "next_action" : "get"
  },
  "data" : {
    "id" : 18388,
    "client_payment_id" : "8ff2a629-e397-42f5-9610-c6dbd12627d0",
    "amount" : 100,
    "total" : 100,
    "created_at" : "2015-02-02T13:31:01.643Z",
    "processed_at" : "2015-02-02T13:32:14.429Z",
    "status" : "completed",
    "type" : "inout",
    "service" : {
      "id" : 834,
      "name" : "Мегафон"
    },
    "parameters" : [ {
      "code" : "phoneNumber",
      "name" : "№ Телефона",
      "value" : "9267101283"
    } ],
    "inbound" : {
      "id" : 4,
      "code" : "ipsp_in",
      "name" : "ООО ИПСП (агент)"
    },
    "outbound" : {
      "id" : 1,
      "code" : "tpr_out",
      "name" : "Кредит Пилот"
    },
    "card" : {
      "id" : 7222,
      "state" : "active",
      "title" : "541715******6825",
      "type" : "MasterCard"
    },
    "wallet" : {
      "phone" : "+79261111111"
    }
  }
}
```
> Платеж успешен. 

## Отложенный выбор типа платежа

Пользователь может отложить выбор типа платежа между `out` и `inout` до подтверждения платежа.
Платеж может быть создан, как платеж со счета кошелька `out`, затем, если клиент передумал и решил заплатить картой, 
при подтверждении платежа вызовом 

`POST` /payments/{:id}/pay 

нужно передать в теле запроса следующие поля:

### Поля запроса подтверждения платежа для смены типа in -> inout

* `type` = inout - тип платежа
* `store_card` - `true` | `false` - сохранить карту, чтобы использовать ее для платежей в дальнейшем (опциональный)
* `card` - идентификатор сохраненной карты с которой будут списаны деньги (опциональный)

> Создаем платеж используя кошелек как источник средств:

```shell
$ curl -u+79261111111:password -H 'Content-type:application/json' 
-d '{"type": "out", "amount": 100, "card": 7222, "service": 834, "parameters": {"phoneNumber": "9267101283"}}' 
https://www.synq.ru/mserver2-dev/v1/payments
```

```json
{
  "meta" : {
    "code" : 200,
    "urgent_data" : {
      "amount" : 10000
    },
    "next_action" : "pay"
  },
  "data" : {
    "id" : 19493,
    "client_payment_id" : "11140dd2-f002-4970-8603-fb48dc299673",
    "amount" : 100,
    "total" : 100.00,
    "created_at" : "2015-02-09T11:36:54.263Z",
    "status" : "created",
    "type" : "out",
    "service" : {
      "id" : 834,
      "name" : "Мегафон"
    },
    "parameters" : [ {
      "code" : "phoneNumber",
      "name" : "№ Телефона",
      "value" : "9267101283"
    } ],
    "outbound" : {
      "id" : 1,
      "code" : "tpr_out",
      "name" : "Кредит Пилот"
    },
    "wallet" : {
      "phone" : "+79261111111"
    }
  }
}
```

> Решаем заплатить сохраненной картой с ID 7222:

```shell
$ curl -u+79261111111:password -H 'Content-type:application/json' 
-d '{"type": "inout", "card": 7222}'
https://www.synq.ru/mserver2-dev/v1/payments/19493/pay
```

```json
{
  "meta" : {
    "code" : 200,
    "urgent_data" : {
      "amount" : 10000
    },
    "next_action" : "get"
  },
  "data" : {
    "id" : 19493,
    "client_payment_id" : "11140dd2-f002-4970-8603-fb48dc299673",
    "amount" : 100,
    "total" : 100,
    "created_at" : "2015-02-09T11:36:54.263Z",
    "status" : "processing",
    "type" : "inout",
    "service" : {
      "id" : 834,
      "name" : "Мегафон"
    },
    "parameters" : [ {
      "code" : "phoneNumber",
      "name" : "№ Телефона",
      "value" : "9267101283"
    } ],
    "inbound" : {
      "id" : 4,
      "code" : "ipsp_in",
      "name" : "ООО ИПСП (агент)"
    },
    "outbound" : {
      "id" : 1,
      "code" : "tpr_out",
      "name" : "Кредит Пилот"
    },
    "card" : {
      "id" : 7222,
      "state" : "active",
      "title" : "541715******6825",
      "type" : "MasterCard"
    },
    "wallet" : {
      "phone" : "+79261111111"
    }
  }
}
```

> Проверяем статус платежа:

```shell
$ curl -u+79261111111:password https://www.synq.ru/mserver2-dev/v1/payments/19493
```

```json
{
  "meta" : {
    "code" : 200,
    "urgent_data" : {
      "amount" : 10000
    },
    "next_action" : "get"
  },
  "data" : {
    "id" : 19493,
    "client_payment_id" : "11140dd2-f002-4970-8603-fb48dc299673",
    "amount" : 100,
    "total" : 100,
    "created_at" : "2015-02-09T11:36:54.263Z",
    "processed_at" : "2015-02-09T11:40:17.546Z",
    "status" : "completed",
    "type" : "inout",
    "service" : {
      "id" : 834,
      "name" : "Мегафон"
    },
    "parameters" : [ {
      "code" : "phoneNumber",
      "name" : "№ Телефона",
      "value" : "9267101283"
    } ],
    "inbound" : {
      "id" : 4,
      "code" : "ipsp_in",
      "name" : "ООО ИПСП (агент)"
    },
    "outbound" : {
      "id" : 1,
      "code" : "tpr_out",
      "name" : "Кредит Пилот"
    },
    "card" : {
      "id" : 7222,
      "state" : "active",
      "title" : "541715******6825",
      "type" : "MasterCard"
    },
    "wallet" : {
      "phone" : "+79261111111"
    },
    "remote_check" : "1423482079404"
  }
}
```
> Платеж успешен. 