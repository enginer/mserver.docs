# Транзитный платёж с карты в сервис

Средства снимаются с указаной карты и переводятся в указаный сервис

### Параметры

* `type` = out - тип платежа
* `client_payment_id` - клиентский идентификатор платежа
* `amount` -  сумма к зачислению
* `service` - назначение платежа, идентификатор Сервиса
* `parameters` - параметры платежного запроса в соответствии с /services/{service}

> Создаём платёж

```shell
$ curl -u+79261111111:password -H 'Content-type:application/json' -d '{"type": "out", "client_payment_id": "e731a7e2-c553-4295-867e-1023359bee33", "amount": 100, "service": 2, "parameters": {"phoneNumber": "9267101283"}}' https://www.synq.ru/mserver2-dev/v1/payments
```

```json
{
  "meta" : {
    "code" : 200,
    "urgent_data" : {
      "amount" : 9925.9
    },
    "next_action" : "pay"
  },
  "data" : {
    "id" : 1401089246342,
    "client_payment_id" : "e731a7e2-c553-4295-867e-1023359bee33",
    "amount" : 100,
    "total" : 110.50,
    "created_at" : "2014-11-15T15:59:05.068Z",
    "status" : "created",
    "type" : "out",
    "service" : {
      "id" : 2,
      "name" : "МТС"
    },
    "parameters" : [ {
      "code" : "phoneNumber",
      "name" : "№ телефона (10 цифр):",
      "value" : "9267101283"
    } ],
    "outbound" : {
      "id" : 35,
      "code" : "tpr_out",
      "name" : "ООО ТПР (провайдер)"
    },
    "wallet" : {
      "phone" : "+380931111111"
    }
  }
}
```

## Оплата с подключённой карты

### Параметр

* `card_id` - идентификатор карты с которой буду сняты средства

> Платим с указанием идентификатора карты с которой желаем оплатить

```shell
$ curl -u+79261111111:password -H 'Content-type:application/json' -d '{"card_id": 3901}' https://www.synq.ru/mserver2-dev/v1/payments/1401089246342/pay
```

```json
{
  "meta" : {
    "code" : 200,
    "urgent_data" : {
      "amount" : 9925.9
    },
    "next_action" : "get"
  },
  "data" : {
    "id" : 1401089246342,
    "client_payment_id" : "e731a7e2-c553-4295-867e-1023359bee33",
    "amount" : 100,
    "total" : 110.5,
    "created_at" : "2014-11-15T15:59:05.068Z",
    "status" : "processing",
    "type" : "out",
    "service" : {
      "id" : 2,
      "name" : "МТС"
    },
    "parameters" : [ {
      "code" : "phoneNumber",
      "name" : "№ телефона (10 цифр):",
      "value" : "9267101283"
    } ],
    "outbound" : {
      "id" : 35,
      "code" : "tpr_out",
      "name" : "ООО ТПР (провайдер)"
    },
    "card" : {
      "id" : 3901,
      "state" : "active",
      "title" : "465206******1342",
      "type" : "Visa"
    },
    "wallet" : {
      "phone" : "+380931111111"
    }
  }
}
```

## Оплата с подключением новой карты

### Параметр

* `new_card` - `true` указать на необходимость подключения новой карты

> Оплата с новой карты

```shell
$ curl -u+79261111111:password -H 'Content-type:application/json' -d '{"new_card": true}' https://www.synq.ru/mserver2-dev/v1/payments/1401089246343/pay
```

```json
{
  "meta" : {
    "code" : 200,
    "urgent_data" : {
      "amount" : 9925.9
    },
    "next_action" : "get"
  },
  "data" : {
    "id" : 1401089246343,
    "client_payment_id" : "e731a7e2-c553-4295-867e-1023359bee34",
    "amount" : 100,
    "total" : 110.5,
    "created_at" : "2014-11-15T16:15:08.076Z",
    "status" : "processing",
    "type" : "out",
    "service" : {
      "id" : 2,
      "name" : "МТС"
    },
    "parameters" : [ {
      "code" : "phoneNumber",
      "name" : "№ телефона (10 цифр):",
      "value" : "9267101283"
    } ],
    "outbound" : {
      "id" : 35,
      "code" : "tpr_out",
      "name" : "ООО ТПР (провайдер)"
    },
    "card" : {
      "id" : 3984,
      "state" : "pending",
      "payment_page_url" : "https://test1.ipsp.com/frontend/endpoint?product_id=1721&desc=mserver2&payment_type=S&amount=1.00&currency=RUB&biller_client_id=0d174fa007014d469fc15de5d6cf29a8&perspayee_expiry=0150&recur_freq=1&locale=ru&hash=78a1fc0b79479531e1b5bf3f371eb9e0ef3e3c7d"
    },
    "wallet" : {
      "phone" : "+380931111111"
    }
  }
}
```

> Далее система ожидает проплаты по ссылке указанной в параметре `payment_page_url`

> Завершённый платёж

```shell
$ curl -u+79261111111:password https://www.synq.ru/mserver2-dev/v1/payments/1401089246342
```

```json
{
  "meta" : {
    "code" : 200,
    "urgent_data" : {
      "amount" : 9925.9
    },
    "next_action" : "get"
  },
  "data" : {
    "id" : 1401089246342,
    "client_payment_id" : "e731a7e2-c553-4295-867e-1023359bee33",
    "amount" : 100,
    "total" : 110.5,
    "created_at" : "2014-11-15T15:59:05.068Z",
    "processed_at" : "2014-11-15T16:06:43.013Z",
    "status" : "completed",
    "type" : "out",
    "service" : {
      "id" : 2,
      "name" : "МТС"
    },
    "parameters" : [ {
      "code" : "phoneNumber",
      "name" : "№ телефона (10 цифр):",
      "value" : "9267101283"
    } ],
    "inbound" : {
      "id" : 62,
      "code" : "ipsp_in",
      "name" : "ООО ИПСП (агент)"
    },
    "outbound" : {
      "id" : 35,
      "code" : "tpr_out",
      "name" : "ООО ТПР (провайдер)"
    },
    "card" : {
      "id" : 3901,
      "state" : "active",
      "title" : "465206******1342",
      "type" : "Visa"
    },
    "wallet" : {
      "phone" : "+380931111111"
    },
    "remote_check" : "1416067592490"
  }
}
```