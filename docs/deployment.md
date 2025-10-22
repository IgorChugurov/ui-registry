# Руководство по деплою

## Обзор

UI Registry можно развернуть на различных платформах. Рекомендуется использовать Vercel для простоты настройки и интеграции с Next.js.

## Vercel (Рекомендуется)

### Автоматический деплой

1. **Подключить репозиторий**:

   - Зайти на [vercel.com](https://vercel.com)
   - Нажать "New Project"
   - Выбрать репозиторий `IgorChugurov/ui-registry`
   - Настроить переменные окружения

2. **Переменные окружения**:

```bash
# Обязательные
NEXT_PUBLIC_REGISTRY_URL=https://your-domain.vercel.app

# Опциональные
REGISTRY_AUTH_TOKEN=your_secret_token_here
NEXT_PUBLIC_VERCEL_URL=https://your-domain.vercel.app
```

3. **Настройки сборки**:

```json
{
  "buildCommand": "npm run build",
  "outputDirectory": ".next",
  "installCommand": "npm install"
}
```

### Ручной деплой

```bash
# Установить Vercel CLI
npm i -g vercel

# Логин
vercel login

# Деплой
vercel

# Продакшн деплой
vercel --prod
```

### Настройка домена

1. **В настройках проекта** Vercel
2. **Добавить кастомный домен**
3. **Настроить DNS** записи
4. **Обновить переменные окружения**

## Netlify

### Настройка

1. **Создать `netlify.toml`**:

```toml
[build]
  command = "npm run build"
  publish = ".next"

[build.environment]
  NODE_VERSION = "22"

[[redirects]]
  from = "/r/*"
  to = "/r/:splat"
  status = 200

[[redirects]]
  from = "/demo/*"
  to = "/demo/:splat"
  status = 200
```

2. **Подключить репозиторий** к Netlify
3. **Настроить переменные окружения**
4. **Деплой** происходит автоматически

### Переменные окружения

```bash
NEXT_PUBLIC_REGISTRY_URL=https://your-domain.netlify.app
REGISTRY_AUTH_TOKEN=your_secret_token_here
```

## Docker

### Dockerfile

```dockerfile
# Используем Node.js 22
FROM node:22-alpine AS base

# Устанавливаем зависимости
FROM base AS deps
RUN apk add --no-cache libc6-compat
WORKDIR /app

# Копируем package файлы
COPY package.json pnpm-lock.yaml* ./
RUN npm install -g pnpm && pnpm install --frozen-lockfile

# Сборка приложения
FROM base AS builder
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .

# Устанавливаем переменные окружения
ENV NEXT_TELEMETRY_DISABLED 1
ENV NODE_ENV production

# Собираем приложение
RUN npm run build

# Продакшн образ
FROM base AS runner
WORKDIR /app

ENV NODE_ENV production
ENV NEXT_TELEMETRY_DISABLED 1

RUN addgroup --system --gid 1001 nodejs
RUN adduser --system --uid 1001 nextjs

COPY --from=builder /app/public ./public
COPY --from=builder --chown=nextjs:nodejs /app/.next/standalone ./
COPY --from=builder --chown=nextjs:nodejs /app/.next/static ./.next/static

USER nextjs

EXPOSE 3000

ENV PORT 3000
ENV HOSTNAME "0.0.0.0"

CMD ["node", "server.js"]
```

### Docker Compose

```yaml
version: "3.8"

services:
  ui-registry:
    build: .
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
      - NEXT_PUBLIC_REGISTRY_URL=http://localhost:3000
      - REGISTRY_AUTH_TOKEN=your_secret_token_here
    volumes:
      - ./public:/app/public:ro
```

### Сборка и запуск

```bash
# Сборка образа
docker build -t ui-registry .

# Запуск контейнера
docker run -p 3000:3000 ui-registry

# Или с docker-compose
docker-compose up -d
```

## AWS

### AWS Amplify

1. **Создать приложение** в AWS Amplify
2. **Подключить репозиторий**
3. **Настроить переменные окружения**
4. **Настроить build settings**:

```yaml
version: 1
frontend:
  phases:
    preBuild:
      commands:
        - npm install
    build:
      commands:
        - npm run build
  artifacts:
    baseDirectory: .next
    files:
      - "**/*"
  cache:
    paths:
      - node_modules/**/*
```

### AWS ECS

1. **Создать Docker образ**
2. **Загрузить в ECR**
3. **Создать ECS сервис**
4. **Настроить ALB** для маршрутизации

## Google Cloud Platform

### Cloud Run

1. **Создать Dockerfile** (см. выше)
2. **Собрать и загрузить образ**:

```bash
# Сборка
docker build -t gcr.io/PROJECT_ID/ui-registry .

# Загрузка
docker push gcr.io/PROJECT_ID/ui-registry
```

3. **Деплой на Cloud Run**:

```bash
gcloud run deploy ui-registry \
  --image gcr.io/PROJECT_ID/ui-registry \
  --platform managed \
  --region us-central1 \
  --allow-unauthenticated
```

## Azure

### Azure Static Web Apps

1. **Создать Static Web App** в Azure
2. **Подключить репозиторий**
3. **Настроить переменные окружения**
4. **Настроить build configuration**:

```yaml
# azure-static-web-apps.yml
app_location: "/"
api_location: ""
output_location: ".next"
app_build_command: "npm run build"
api_build_command: ""
```

## Настройка домена

### Кастомный домен

1. **Купить домен** у регистратора
2. **Настроить DNS** записи:

```
# Для Vercel
CNAME www your-domain.vercel.app
CNAME @ your-domain.vercel.app

# Для Netlify
CNAME www your-domain.netlify.app
CNAME @ your-domain.netlify.app
```

3. **Обновить переменные окружения**:

```bash
NEXT_PUBLIC_REGISTRY_URL=https://your-domain.com
```

### SSL сертификат

- **Vercel/Netlify**: автоматически
- **Docker**: настроить в reverse proxy
- **AWS**: использовать ACM
- **GCP**: автоматически в Cloud Run

## Мониторинг

### Vercel Analytics

```typescript
// src/app/layout.tsx
import { Analytics } from "@vercel/analytics/react";

export default function RootLayout({ children }) {
  return (
    <html>
      <body>
        {children}
        <Analytics />
      </body>
    </html>
  );
}
```

### Логирование

```typescript
// src/lib/logger.ts
export function log(message: string, data?: any) {
  if (process.env.NODE_ENV === "development") {
    console.log(message, data);
  }

  // В продакшне отправлять в сервис логирования
  if (process.env.NODE_ENV === "production") {
    // Отправить в Sentry, LogRocket, etc.
  }
}
```

### Мониторинг производительности

```typescript
// src/lib/metrics.ts
export function trackPageView(page: string) {
  // Отправить метрики в аналитику
  if (typeof window !== "undefined") {
    // Google Analytics, Mixpanel, etc.
  }
}
```

## Безопасность

### Защита JSON API

```typescript
// src/middleware.ts
import { NextResponse } from "next/server";
import type { NextRequest } from "next/server";

export function middleware(request: NextRequest) {
  // Проверка токена для /r/* маршрутов
  if (request.nextUrl.pathname.startsWith("/r/")) {
    const token = request.nextUrl.searchParams.get("token");

    if (token !== process.env.REGISTRY_AUTH_TOKEN) {
      return new NextResponse("Unauthorized", { status: 401 });
    }
  }

  return NextResponse.next();
}

export const config = {
  matcher: "/r/:path*",
};
```

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
          {
            key: "Access-Control-Allow-Methods",
            value: "GET, OPTIONS",
          },
        ],
      },
    ];
  },
};
```

### Rate Limiting

```typescript
// src/lib/rate-limit.ts
import { NextRequest } from "next/server";

const rateLimit = new Map();

export function checkRateLimit(request: NextRequest) {
  const ip = request.ip || "unknown";
  const now = Date.now();
  const windowMs = 15 * 60 * 1000; // 15 минут
  const maxRequests = 100;

  if (!rateLimit.has(ip)) {
    rateLimit.set(ip, { count: 1, resetTime: now + windowMs });
    return true;
  }

  const data = rateLimit.get(ip);

  if (now > data.resetTime) {
    rateLimit.set(ip, { count: 1, resetTime: now + windowMs });
    return true;
  }

  if (data.count >= maxRequests) {
    return false;
  }

  data.count++;
  return true;
}
```

## Оптимизация

### CDN

- **Vercel**: автоматически
- **Netlify**: автоматически
- **Docker**: настроить CloudFlare, AWS CloudFront

### Кэширование

```typescript
// next.config.ts
const nextConfig = {
  async headers() {
    return [
      {
        source: "/r/:path*",
        headers: [
          {
            key: "Cache-Control",
            value: "public, max-age=31536000, immutable",
          },
        ],
      },
    ];
  },
};
```

### Сжатие

```typescript
// next.config.ts
const nextConfig = {
  compress: true,
  poweredByHeader: false,
};
```

## Troubleshooting

### Общие проблемы

#### Сборка не проходит

```bash
# Очистить кэш
rm -rf .next node_modules
npm install
npm run build
```

#### JSON API не работает

1. Проверить, что `registry:build` выполнился
2. Проверить права доступа к файлам
3. Проверить переменные окружения

#### Стили не применяются

1. Проверить Tailwind конфигурацию
2. Проверить порядок импорта CSS
3. Проверить CSS переменные

### Логи

```bash
# Vercel
vercel logs

# Docker
docker logs container_name

# AWS
aws logs describe-log-groups
```

### Мониторинг

- **Uptime**: Pingdom, UptimeRobot
- **Performance**: Lighthouse, WebPageTest
- **Errors**: Sentry, LogRocket
- **Analytics**: Google Analytics, Mixpanel
