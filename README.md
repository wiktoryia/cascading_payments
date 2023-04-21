# Каскадные платежи 
  
**Каскадные платежи** - способ осуществления платежей, при котором только что отклоненный платеж проводится через альтернативный шлюз.  
**Шлюз** - сервис для обработки электронных транзакций, обеспечивающий контакт между продавцом, покупателем и банком. 

Каскадные платежи используются для увеличения конверсии платежей.    
Для работы каскадных платежей необходимо, чтобы для магазина были подключены несколько равноценных шлюзов для приема платежей (например, оба шлюза могут принимать платежи по карте Visa). Тогда, если первый запрос на оплату вернет статус `failed`, то вы можете повторить запрос, и система перенаправит запрос на второй шлюз, на котором есть вероятность успешно обработать транзакцию.  
Каскадные платежи работают только с типом транзакций <a href="https://doc.begateway.com/ru/integration/card_api/transactions/payment/" target="_blank">оплата</a> и <a href="https://doc.begateway.com/ru/integration/card_api/transactions/authorization/" target="_blank">авторизация</a> и только со статусом транзакции `failed` (со статусами транзакций `expired` и `incomplete` не работают).  
# Как пользоваться каскадными платежами 
**Шаг 1** - Выполнить запрос <a href="https://doc.begateway.com/ru/integration/card_api/transactions/payment/" target="_blank">оплаты</a> или <a href="https://doc.begateway.com/ru/integration/card_api/transactions/authorization/" target="_blank">авторизации</a>.  
**Шаг 2** - Если в ответе возвращен статус транзакции `failed`, добавить в запрос массив `excluded_gateways`, содержащий в себе `gateway_id` шлюза, который вернул `failed` статус, установить параметр `"duplicate_check": false` и повторить запрос.  
**Шаг 3** - Если в ответе снова получен статус транзакции `failed`, повторить **Шаг 2**; если статус транзакции `successful` - это означает, что транзакция успешно проведена.    

**ЗАПРОС**
  
  
Параметры запроса такие же как у запросов <a href="https://doc.begateway.com/ru/integration/card_api/transactions/payment/" target="_blank">оплата</a> и <a href="https://doc.begateway.com/ru/integration/card_api/transactions/authorization/" target="_blank">авторизация</a>, за исключением следующих параметров:  
Параметр | Тип | Описание
-------- | --- | --------
`additional_data` | object |  
`excluded_gateways`</br>*обязательный| массив | Массив, содержащий `gateway_id` шлюза/шлюзов, которые вернули `failed` статус во время предыдущих запросов. Параметр `gateway_id` возвращается в каждом ответе.    

**ОТВЕТ**
```json
{
  "request": {
    "dynamic_billing_descriptor": nil,
    "billing_address": {
      "first_name": nil,
      "last_name": nil,
      "address": "Baker street 221b",
      "city": "London",
      "country": "GB",
      "state": nil,
      "phone": nil,
      "zip": nil,
      "email": "example@example.com",
      "birth_date": nil,
      "device_id": nil
    },
    "customer": {
      "ip": "127.0.0.1",
      "email": "example@example.com"
    },
    "test": false,
    "notification_url": nil,
    "return_url": "https://example.com/widget/confirm.html?token=e3266138b6bf0789d4bb12f584130bbe8d181199682dc3fc86b1df38c94c3a",
    "language": "ru",
    "credit_card": {
      "number": "5 XXXX 9423",
      "verification_value": "[FILTERED]",
      "holder": "John Doe",
      "save_card": false,
      "exp_month": "08",
      "exp_year": "2022"
    },
    "currency": "BYN",
    "amount": 100,
    "description": "Test async #1",
    "tracking_id": "async-123",
    "additional_data": {
      "request_id": "f3c809d1-6dfe-41f8-b803-6a15782e3de0",
      "browser": {
        "screen_width": 1680,
        "screen_height": 1050,
        "screen_color_depth": 24,
        "language": "ru",
        "java_enabled": false,
        "user_agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.114 Safari/537.36",
        "time_zone": -180,
        "accept_header": "application/json"
      },
      "vendor": {
        "name": "CTP",
        "token": "e3266138b6bfb0789d4bb12f584130bbe8dd181199682dc3fc86b1df38c94c3a"
      },
      "excluded_gateways": [
        "408",
        "409"
      ]
    },
    "expired_at": nil,
    "duplicate_check": false
  }
}



