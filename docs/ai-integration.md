# Интеграция с AI-инструментами

## Обзор

UI Registry интегрируется с различными AI-инструментами для ускорения разработки и предоставления контекста для генерации кода.

## Поддерживаемые платформы

### 1. v0.dev (Vercel)

**Кнопка "Open in v0"** на каждой странице компонента.

#### Как это работает

1. **URL генерация**:

```typescript
const v0Url = `https://v0.dev/chat/api/open?url=https%3A%2F%2Fui-registry-lyart.vercel.app%2Fr%2F${componentName}.json`;
```

2. **Передача контекста**:

- JSON файл компонента загружается в v0
- v0 получает код компонента и метаданные
- Пользователь может сразу начать чат с контекстом

#### Пример интеграции

```tsx
// src/components/registry/open-in-v0.tsx
export function OpenInV0({ componentName }: { componentName: string }) {
  const v0Url = `https://v0.dev/chat/api/open?url=https%3A%2F%2Fui-registry-lyart.vercel.app%2Fr%2F${componentName}.json`;

  return (
    <Button asChild>
      <a href={v0Url} target="_blank" rel="noopener noreferrer">
        <ExternalLink className="mr-2 h-4 w-4" />
        Open in v0
      </a>
    </Button>
  );
}
```

### 2. Cursor / Windsurf (MCP)

**Model Context Protocol** интеграция для автодополнения.

#### Настройка MCP

**Для Cursor** (`cursor-settings.json`):

```json
{
  "mcpServers": {
    "shadcn": {
      "command": "npx -y shadcn@canary registry:mcp",
      "env": {
        "REGISTRY_URL": "https://ui-registry-lyart.vercel.app/r/registry.json"
      }
    }
  }
}
```

**Для Windsurf** (`windsurf-settings.json`):

```json
{
  "mcpServers": {
    "shadcn": {
      "command": "npx -y shadcn@canary registry:mcp",
      "env": {
        "REGISTRY_URL": "https://ui-registry-lyart.vercel.app/r/registry.json"
      }
    }
  }
}
```

#### Использование MCP

После настройки MCP, AI может:

- Автоматически предлагать компоненты из реестра
- Генерировать код с использованием ваших компонентов
- Понимать контекст вашей дизайн-системы

### 3. GitHub Copilot

**Прямая интеграция** через JSON API.

#### Настройка

1. **Добавить в .copilot-ignore**:

```
# Исключить из копирования
src/app/demo/
public/r/
```

2. **Использовать в промптах**:

```
Используй компоненты из https://ui-registry-lyart.vercel.app/r/button.json
```

## API для AI-инструментов

### JSON API

**Базовый URL**: `https://ui-registry-lyart.vercel.app/r/`

#### Получение компонента

```bash
curl https://ui-registry-lyart.vercel.app/r/button.json
```

**Ответ**:

```json
{
  "name": "button",
  "type": "registry:ui",
  "title": "Button",
  "description": "Allows users to take actions",
  "files": [
    {
      "path": "src/layouts/minimal-layout.tsx",
      "content": "/* код компонента */",
      "type": "registry:file",
      "target": "app/layout.tsx"
    }
  ]
}
```

#### Получение всего реестра

```bash
curl https://ui-registry-lyart.vercel.app/r/registry.json
```

**Ответ**: полный `registry.json` файл.

### Аутентификация

Для защиты JSON API используется токен:

```typescript
// src/middleware.ts
export function middleware(request: NextRequest) {
  const token = request.nextUrl.searchParams.get("token");

  if (token !== process.env.REGISTRY_AUTH_TOKEN) {
    return new NextResponse("Unauthorized", { status: 401 });
  }

  return NextResponse.next();
}
```

**Использование с токеном**:

```bash
curl "https://ui-registry-lyart.vercel.app/r/button.json?token=YOUR_TOKEN"
```

## Конфигурация для разных платформ

### Vercel v0

**Настройка в v0**:

1. Перейти в настройки проекта
2. Добавить registry URL: `https://ui-registry-lyart.vercel.app/r/registry.json`
3. Включить автозагрузку компонентов

### Cursor

**Настройка MCP**:

1. Открыть настройки Cursor
2. Добавить MCP сервер с командой выше
3. Перезапустить Cursor

### Windsurf

**Аналогично Cursor**:

1. Настройки → MCP Servers
2. Добавить shadcn MCP сервер
3. Перезапустить Windsurf

## Лучшие практики

### Для разработчиков

1. **Используйте осмысленные названия** компонентов
2. **Документируйте пропсы** в комментариях
3. **Включайте примеры использования** в демо
4. **Тестируйте интеграцию** с AI-инструментами

### Для AI-промптов

1. **Указывайте registry URL** в промптах
2. **Используйте конкретные названия** компонентов
3. **Включайте контекст** дизайн-системы
4. **Проверяйте результаты** генерации

## Примеры использования

### С v0.dev

```
Создай форму входа используя компоненты из https://ui-registry-lyart.vercel.app/r/login.json
```

### С Cursor

```
Используй button компонент из нашего реестра для создания навигации
```

### С Windsurf

```
Создай dashboard используя блоки из https://ui-registry-lyart.vercel.app/r/dashboard.json
```

## Отладка интеграции

### Проверка JSON API

```bash
# Проверить доступность
curl -I https://ui-registry-lyart.vercel.app/r/button.json

# Проверить содержимое
curl https://ui-registry-lyart.vercel.app/r/button.json | jq
```

### Проверка MCP

```bash
# Проверить MCP сервер
npx shadcn@canary registry:mcp --help

# Тест подключения
npx shadcn@canary registry:mcp --registry https://ui-registry-lyart.vercel.app/r/registry.json
```

### Логи ошибок

Проверьте консоль браузера и логи сервера на ошибки интеграции.

## Безопасность

### Защита API

1. **Используйте токены** для защиты JSON API
2. **Ограничьте доступ** по IP если необходимо
3. **Мониторьте использование** API

### CORS настройки

```typescript
// next.config.ts
const nextConfig = {
  async headers() {
    return [
      {
        source: "/r/:path*",
        headers: [
          {
            key: "Access-Control-Allow-Origin",
            value: "*",
          },
        ],
      },
    ];
  },
};
```

## Производительность

### Кэширование

- JSON файлы кэшируются на CDN
- Используйте статическую генерацию
- Минимизируйте размер JSON файлов

### Оптимизация

- Сжимайте JSON файлы
- Используйте CDN для статических файлов
- Мониторьте время ответа API

## Мониторинг

### Метрики использования

- Количество запросов к JSON API
- Популярные компоненты
- Ошибки интеграции

### Аналитика

```typescript
// Добавить аналитику
import { Analytics } from "@vercel/analytics";

export function trackComponentUsage(componentName: string) {
  Analytics.track("component_used", {
    component: componentName,
    source: "ai_integration",
  });
}
```
