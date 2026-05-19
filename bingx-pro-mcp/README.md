# BingX Pro MCP Server

Полнофункциональный MCP-сервер для интеграции с криптобиржей **BingX**. Предоставляет AI-ассистентам (Windsurf, Cursor, Claude Code) доступ к торговым API BingX с встроенным риск-менеджментом и уведомлениями в Telegram.

## 🚀 Возможности

### Торговые инструменты
- **Spot Trading**: ордера, отмены, история сделок
- **Swap/Futures Trading**: ордера, позиции, плечо, закрытие позиций
- **Advanced Orders**: условные ордера (stop/take-profit), OCO
- **Batch Operations**: пакетное размещение и отмена ордеров

### Рыночные данные
- Тикеры, свечи OHLCV, стакан заявок
- Ставки финансирования, открытый интерес, маркировочная цена
- **WebSocket**: реал-тайм подписки на тикеры, свечи, глубину

### Риск-менеджмент (КРИТИЧНО)
- **Kill-Switch**: мгновенная блокировка всех торговых операций
- **Лимиты**: размер позиции, кредитное плечо, частота ордеров
- **Daily Drawdown**: автоматическая остановка при просадке за день
- **Dry Run Mode**: симуляция без реальных ордеров

### Уведомления
- Telegram-бот для алертов
- События: исполнение ордеров, срабатывание стопов, активация kill-switch
- Форматированные сообщения с Markdown

## 📁 Структура проекта

```
bingx-pro-mcp/
├── src/
│   ├── index.ts                    # Точка входа
│   ├── transport.ts                # MCP Stdio транспорт
│   ├── client/
│   │   ├── BingXClient.ts          # REST API клиент
│   │   ├── BingXWebSocket.ts       # WebSocket менеджер
│   │   └── types.ts                # TypeScript типы
│   ├── tools/
│   │   ├── definitions.ts          # MCP Tool определения
│   │   └── handlers.ts             # Обработчики инструментов
│   ├── modules/
│   │   ├── risk-manager/           # Риск-движок
│   │   └── notifier/               # Telegram уведомления
│   ├── utils/                      # Утилиты
│   └── config.ts                   # Конфигурация
├── test-mcp.ts                     # Тестовый клиент
├── package.json
├── tsconfig.json
└── .env.example
```

## ⚙️ Установка

### 1. Клонирование и установка зависимостей

```bash
cd bingx-pro-mcp
npm install
```

### 2. Настройка окружения

Создайте файл `.env` на основе `.env.example`:

```bash
cp .env.example .env
```

Заполните переменные:

```env
# BingX API (обязательно)
BINGX_API_KEY=your_api_key_here
BINGX_SECRET_KEY=your_secret_key_here
BINGX_BASE_URL=https://open-api.bingx.com
BINGX_WS_URL=wss://open-api.bingx.com/openApi/quote/ws/v2

# Telegram Notifications (опционально)
TELEGRAM_BOT_TOKEN=123456:ABC-DEF1234ghIkl-zyx57W2v1u123ew11
TELEGRAM_CHAT_ID=987654321

# Risk Manager (рекомендуется)
RISK_MAX_POSITION_USDT=500
RISK_MAX_LEVERAGE=5
RISK_MAX_DAILY_DRAWDOWN_PERCENT=5
RISK_ORDER_RATE_LIMIT_PER_MIN=10

# General
LOG_LEVEL=info
DRY_RUN=false
```

### 3. Сборка проекта

```bash
npm run build
```

## 🏃 Запуск

### Продакшен режим

```bash
npm start
```

### Режим разработки

```bash
npm run dev
```

## 🔌 Подключение к AI-ассистентам

### Windsurf / Cursor

Добавьте конфигурацию MCP сервера в настройки вашего AI-ассистента:

```json
{
  "mcpServers": {
    "bingx-pro": {
      "command": "node",
      "args": ["/path/to/bingx-pro-mcp/dist/index.js"],
      "cwd": "/path/to/bingx-pro-mcp",
      "env": {
        "BINGX_API_KEY": "your_key",
        "BINGX_SECRET_KEY": "your_secret"
      }
    }
  }
}
```

### Claude Code

```bash
claude mcp add bingx-pro -- node /path/to/bingx-pro-mcp/dist/index.js
```

## 🛠️ Доступные инструменты

### Account & Balance
- `get_account_info` — информация об аккаунте
- `get_spot_balance` — баланс спота
- `get_swap_balance` — баланс фьючерсов
- `transfer_asset` — перевод между кошельками

### Market Data
- `get_ticker` — текущая цена
- `get_klines` — свечи OHLCV
- `get_order_book` — стакан
- `get_funding_rate` — ставка финансирования
- `get_open_interest` — открытый интерес
- `get_mark_price` — маркировочная цена
- `subscribe_to_ws` — подписка на WebSocket
- `unsubscribe_from_ws` — отписка от WebSocket

### Spot Trading
- `place_spot_order` — разместить ордер
- `cancel_spot_order` — отменить ордер
- `get_spot_open_orders` — активные ордера
- `get_spot_trades` — история сделок

### Swap/Futures (ПРИОРИТЕТ)
- `place_swap_order` — разместить фьючерсный ордер
- `cancel_swap_order` — отменить ордер
- `get_swap_open_orders` — активные ордера
- `get_position` / `get_positions_all` — позиции
- `close_position` — закрыть позицию по рынку
- `set_leverage` — установить плечо

### Advanced Orders
- `place_conditional_order` — стоп/триггер ордер
- `place_oco_order` — OCO ордер
- `get_conditional_orders` — список условных ордеров

### Risk & Management
- `get_account_risk` — уровень риска
- `get_pnl_history` — история PnL
- `enable_risk_rule` / `disable_risk_rule` — управление правилами
- `get_risk_status` — статус риск-движка
- `send_telegram_test` — тестовое уведомление
- `batch_place_orders` / `batch_cancel_orders` — пакетные операции
- `test_order` — тестовый ордер (валидация без исполнения)

## 🧪 Тестирование

Запустите тестовый клиент:

```bash
npm run build
npx ts-node test-mcp.ts
```

Тесты проверяют:
1. Подключение к MCP серверу
2. Получение тикера (REST API)
3. WebSocket подписку
4. Валидацию риск-движка (лимит плеча)
5. Статус риск-менеджмента
6. Отправку Telegram уведомления

## 🔒 Безопасность

- **API ключи** хранятся только в `.env`, никогда не логируются
- **HMAC-SHA256 подпись** всех запросов
- **HTTPS** для всех REST вызовов
- **Retry-логика** с exponential backoff для 429/5xx ошибок

## 🛡️ Риск-менеджмент

### Правила по умолчанию

| Правило | Значение | Описание |
|---------|----------|----------|
| MAX_POSITION_SIZE | 500 USDT | Максимальный размер позиции |
| MAX_LEVERAGE | 5x | Максимальное плечо |
| DAILY_DRAWDOWN_KILL | 5% | Просадка за день для kill-switch |
| ORDER_RATE_LIMIT | 10/мин | Лимит ордеров в минуту |

### Kill-Switch

Активируется автоматически при:
- Превышении дневной просадки
- Критических ошибках системы

Требует **ручного сброса** через перезапуск сервера или модификацию `risk_state.json`.

### Dry Run Mode

При `DRY_RUN=true`:
- Все торговые вызовы логируются
- Ордера **НЕ отправляются** на биржу
- Идеально для тестирования стратегий

## 📊 Логирование

Все логи выводятся в **stderr** (stdout занят MCP транспортом):

```
[2024-01-15T10:30:00.000Z] [BingX-MCP] [RISK_ENGINE] [INFO] Order validated
[2024-01-15T10:30:01.000Z] [BingX-MCP] [TELEGRAM] [INFO] Notification sent
```

Уровни: `debug`, `info`, `warn`, `error`

## 🐛 Обработка ошибок

- Ошибки API возвращаются с `isError: true`
- Структурированный формат ошибок
- Автоматический retry для временных проблем
- Graceful degradation при падении модулей

## 📝 Примеры использования

### Получить цену BTC

```
Call tool: get_ticker
Arguments: { "symbol": "BTC-USDT" }
```

### Разместить фьючерсный ордер

```
Call tool: place_swap_order
Arguments: {
  "symbol": "BTC-USDT-SWAP",
  "side": "BUY",
  "type": "LIMIT",
  "quantity": "0.001",
  "price": "45000",
  "positionSide": "LONG",
  "leverage": 3
}
```

### Проверить риск перед ордером

```
Call tool: test_order
Arguments: {
  "symbol": "BTC-USDT-SWAP",
  "side": "BUY",
  "type": "MARKET",
  "quantity": "0.01",
  "leverage": 10
}
```

## ⚠️ Важные замечания

1. **Не используйте `console.log()`** в runtime-коде — ломает MCP stdio
2. **Risk Engine — первый фильтр** перед любым торговым действием
3. **WebSocket авто-реконнект** с exponential backoff (макс 5 попыток)
4. **Состояние риск-движка** сохраняется в `risk_state.json`

## 📄 Лицензия

MIT

## 🔗 Ссылки

- [Документация BingX API](https://bingx-api.github.io/docs/)
- [AI Skills BingX](https://github.com/BingX-API/api-ai-skills)
- [MCP SDK](https://github.com/modelcontextprotocol/sdk)
