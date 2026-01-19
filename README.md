1.	Написать пример REST API запроса, который будет вызываться при переходе пользователя на данный экран.

Ключевые параметры:
1.	Геолокация пользователя – для определения ближайшей доставки и магазина.
2.	Текущее время.
3.	ID пользователя/сессии – для учёта его предпочтений или истории.

Пример REST API запроса 1 (GET): 
GET /api/v1/partner-stores?lat=55.751244&lon=37.618423&timezone=Asia/Omsk
Host: api.petrushka.zelenaya
Authorization: Bearer <user_jwt_token>
Accept: application/json

Пример REST API запроса 2 (POST): 
POST /api/v1/partner-stores/nearby
Host: api.petrushka.zelenaya
Authorization: Bearer <user_jwt_token>
Content-Type: application/json
Accept: application/json

{
  "user_location": {
    "latitude": 52.714228,
    "longitude": 11.072001
  },
  "delivery_time_preference": "soonest", 
  "include_all_partners": false 
}
Latitude, longitude – геолокация пользователя. Необходимы для расчёта возможности доставки и времени.
Timezone – часовой пояс пользователя для корректного отображения времени.
Дополнительные параметры: delivery_time_preference (предпочтительные сроки доставки), include_all_partners (указание всех партнеров).

2.	Привести пример ответа этого REST API в соответствии с макетом. Формат - JSON. Учесть, что при клике на плашку магазина должен осуществляться переход по ссылке на внешний ресурс.

{
  "success": true,
  "stores": [
    {
      "id": "metro_001",
      "partner_name": "METRO",
      "display_name": "METRO",
      "logo_url": "https://cdn.petrushka.zelenaya/partners/metro/logo.png",
      "delivery_info": {
        "available": true,
        "type": "scheduled",
        "status_text": "Ближайшая доставка сегодня 21:00-23:00",
        "next_slot": {
          "date": "2026-01-14",
          "start_time": "21:00",
          "end_time": "23:00",
          "is_today": true
        },
        "delivery_speed_minutes": null
      },
      "metadata": {
        "sort_priority": 1,
        "distance_km": 2.1,
        "rating": 4.7
      },
      "action": {
        "type": "external_link",
        "url": "https://metro.petrushka.zelenaya/redirect?store_id=001&user_token={USER_TOKEN}",
        "method": "GET"
      },
      "visual_hints": {
        "badge": null,
        "highlighted": false
      }
    },
    {
      "id": "ashan_002",
      "partner_name": "Auchan",
      "display_name": "Ашан",
      "logo_url": "https://cdn.petrushka.zelenaya/partners/auchan/logo.png",
      "delivery_info": {
        "available": true,
        "type": "scheduled",
        "status_text": "Ближайшая доставка сегодня 18:00-20:00",
        "next_slot": {
          "date": "2026-01-14",
          "start_time": "18:00",
          "end_time": "20:00",
          "is_today": true
        },
        "delivery_speed_minutes": null
      },
      "metadata": {
        "sort_priority": 2,
        "distance_km": 1.5,
        "rating": 4.5
      },
      "action": {
        "type": "external_link",
        "url": "https://auchan.petrushka.zelenaya/launch?shop_id=002&partner_key=XYZ123",
        "method": "GET"
      },
      "visual_hints": {
        "badge": null,
        "highlighted": false
      }
    },
    {
      "id": "vkusvill_003",
      "partner_name": "VkusVill",
      "display_name": "ВкусВилл",
      "logo_url": "https://cdn.petrushka.zelenaya/partners/vkusvill/logo.png",
      "delivery_info": {
        "available": true,
        "type": "express",
        "status_text": "Быстрая доставка от 20 до 60 минут",
        "next_slot": null,
        "delivery_speed_minutes": {
          "min": 20,
          "max": 60
        }
      },
      "metadata": {
        "sort_priority": 3,
        "distance_km": 0.8,
        "rating": 4.9
      },
      "action": {
        "type": "external_link",
        "url": "vkusvill://partner.petrushka.zelenaya/store/003",
        "method": "DEEP_LINK",
        "fallback_url": "https://vkusvill.ru/petrushka/redirect?store=003"
      },
      "visual_hints": {
        "badge": "fast",
        "highlighted": true
      }
    },
    {
      "id": "victoria_004",
      "partner_name": "Victoria",
      "display_name": "ВИКТОРИЯ",
      "logo_url": "https://cdn.petrushka.zelenaya/partners/victoria/logo.png",
      "delivery_info": {
        "available": true,
        "type": "scheduled",
        "status_text": "Ближайшая доставка сегодня 17:00-19:00",
        "next_slot": {
          "date": "2026-01-14",
          "start_time": "17:00",
          "end_time": "19:00",
          "is_today": true
        },
        "delivery_speed_minutes": null
      },
      metadata: {
        "sort_priority": 4,
        "distance_km": 2.8,
        "rating": 4.3
      },
      "action": {
        "type": "external_link",
        "url": "https://victoria-market.ru/integration/petrushka?branch_id=004",
        "method": "GET"
      },
      "visual_hints": {
        "badge": null,
        "highlighted": false
      }
    }
  ],
  "user_context": {
    "location_detected": true,
    "coordinates": {
      "latitude": 52.714228,
      "longitude": 11.072001
    },
    "timezone": "Asia/Omsk",
    "current_time": "2026-01-14T09:41:00+06:00"
  }
}
1.	delivery_info.status_text – содержит готовый текст для отображения в интерфейсе, как показано в макете. 
2.	delivery_info.type – позволяет фронтенду стилизовать плашки по-разному:
"scheduled" – для магазинов с временными слотами;
"express" – для быстрой доставки.
3.	action – поле для реализации перехода:
type: "external_link" – указывает, что переход ведет вовне;
url – содержит полную ссылку для перехода;
method: "DEEP_LINK" – указание на попытку открыть мобильное приложение партнера с fallback_url на веб-версию;
Для безопасности в URL могут подставляться токены сессии ({USER_TOKEN}). 
4.	visual_hints – позволяет фронтенду добавить визуальные акценты:
badge: "fast" – для отображения бейджа «Быстро»;
highlighted: true – для выделения карточки. 
5.	Сортировка – магазины уже приходят отсортированными в порядке приоритета (sort_priority), соответствующем макету.  
6.	Контекст пользователя – содержит информацию о том, на основе каких данных был сформирован ответ (геолокация, время), что полезно для отладки и понимания логики работы. 
