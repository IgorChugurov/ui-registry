# Руководство по разработке

## Настройка окружения

### Требования

- **Node.js** >= 22
- **pnpm** 9.15.2 (рекомендуется)
- **Git** для версионирования

### Установка

```bash
# Клонировать репозиторий
git clone https://github.com/IgorChugurov/ui-registry.git
cd ui-registry

# Установить зависимости
pnpm install

# Запустить в режиме разработки
pnpm dev
```

### Переменные окружения

Создайте `.env.local`:

```bash
# Для защиты JSON API (опционально)
REGISTRY_AUTH_TOKEN=your_secret_token_here

# URL реестра
NEXT_PUBLIC_REGISTRY_URL=https://ui-registry-lyart.vercel.app
```

## Структура проекта

### Основные папки

```
src/
├── app/                    # Next.js App Router
│   ├── (registry)/        # Группа маршрутов реестра
│   ├── demo/              # Демо-страницы
│   └── globals.css        # Глобальные стили
├── components/            # React компоненты
│   ├── ui/               # Shadcn/ui компоненты
│   ├── registry/         # Компоненты реестра
│   └── [brand-components] # Брендовые компоненты
├── lib/                   # Утилиты и логика
└── layouts/               # Лейауты для v0
```

### Конфигурационные файлы

- `registry.json` - основной файл реестра
- `components.json` - конфигурация Shadcn/ui
- `next.config.ts` - настройки Next.js
- `tailwind.config.js` - настройки Tailwind CSS

## Разработка компонентов

### Создание UI примитива

1. **Создать компонент** в `src/components/ui/`:

```typescript
// src/components/ui/my-button.tsx
import { Button } from "@/components/ui/button";

export function MyButton({ children, ...props }) {
  return <Button {...props}>{children}</Button>;
}
```

2. **Добавить в registry.json**:

```json
{
  "name": "my-button",
  "type": "registry:ui",
  "title": "My Button",
  "description": "Custom button component",
  "registryDependencies": ["button", "theme"]
}
```

3. **Создать демо** в `src/app/demo/[name]/ui/`:

```typescript
// src/app/demo/[name]/ui/my-button.tsx
import { MyButton } from "@/components/ui/my-button";

export const myButton = {
  name: "my-button",
  components: {
    Default: <MyButton>Click me</MyButton>,
    Variant: <MyButton variant="outline">Outline</MyButton>,
  },
};
```

4. **Добавить в центральный экспорт**:

```typescript
// src/app/demo/[name]/index.tsx
import { myButton } from "@/app/demo/[name]/ui/my-button";

export const demos = {
  // ... existing demos
  "my-button": myButton,
};
```

5. **Пересобрать реестр**:

```bash
npm run registry:build
```

### Создание составного компонента

1. **Создать компонент** в `src/components/`:

```typescript
// src/components/hero-section.tsx
import { Button } from "@/components/ui/button";
import { Card } from "@/components/ui/card";

interface HeroSectionProps {
  title: string;
  description: string;
  buttonText: string;
  buttonLink: string;
}

export function HeroSection({
  title,
  description,
  buttonText,
  buttonLink,
}: HeroSectionProps) {
  return (
    <Card className="p-8">
      <h1 className="text-3xl font-bold">{title}</h1>
      <p className="text-muted-foreground mt-4">{description}</p>
      <Button asChild className="mt-6">
        <a href={buttonLink}>{buttonText}</a>
      </Button>
    </Card>
  );
}
```

2. **Добавить в registry.json**:

```json
{
  "name": "hero-section",
  "type": "registry:component",
  "title": "Hero Section",
  "description": "Hero section with title and button",
  "registryDependencies": ["button", "card", "theme"]
}
```

3. **Создать демо**:

```typescript
// src/app/demo/[name]/components/hero-section.tsx
import { HeroSection } from "@/components/hero-section";

export const heroSection = {
  name: "hero-section",
  components: {
    Default: (
      <HeroSection
        title="Welcome to Our Platform"
        description="Build amazing applications with our components"
        buttonText="Get Started"
        buttonLink="#signup"
      />
    ),
    Minimal: (
      <HeroSection
        title="Simple Hero"
        description="A minimal hero section"
        buttonText="Learn More"
        buttonLink="#learn"
      />
    ),
  },
};
```

### Создание блока страницы

1. **Создать страницу** в `src/app/demo/[name]/blocks/`:

```typescript
// src/app/demo/[name]/blocks/landing-page.tsx
import { HeroSection } from "@/components/hero-section";
import { Card } from "@/components/ui/card";

export function LandingPage() {
  return (
    <div className="min-h-screen">
      <HeroSection
        title="Welcome"
        description="Get started with our platform"
        buttonText="Sign Up"
        buttonLink="#signup"
      />
      <div className="container mx-auto p-8">
        <Card className="p-6">
          <h2 className="text-2xl font-bold">Features</h2>
          <p className="text-muted-foreground mt-2">
            Discover what makes us different
          </p>
        </Card>
      </div>
    </div>
  );
}
```

2. **Добавить в registry.json**:

```json
{
  "name": "landing-page",
  "type": "registry:block",
  "title": "Landing Page",
  "description": "Complete landing page with hero and features",
  "registryDependencies": ["hero-section", "card", "theme"]
}
```

3. **Создать демо**:

```typescript
// src/app/demo/[name]/blocks/landing-page.tsx
import LandingPage from "@/app/demo/[name]/blocks/landing-page";

export const landingPage = {
  name: "landing-page",
  components: {
    Default: <LandingPage />,
  },
};
```

## Работа с темами

### Изменение цветовой схемы

1. **Отредактировать CSS переменные** в `src/app/globals.css`:

```css
:root {
  --background: oklch(0.98 0.01 80.72);
  --foreground: oklch(0.2 0.04 30.2);
  --primary: oklch(0.6 0.15 144.17);
  /* ... остальные переменные */
}
```

2. **Обновить registry.json**:

```json
{
  "name": "theme",
  "type": "registry:theme",
  "cssVars": {
    "light": {
      "background": "oklch(0.98 0.01 80.72)",
      "foreground": "oklch(0.2 0.04 30.2)",
      "primary": "oklch(0.6 0.15 144.17)"
    }
  }
}
```

3. **Пересобрать**:

```bash
npm run registry:build
```

### Добавление новых цветов

1. **Добавить в CSS**:

```css
:root {
  --brand: oklch(0.5 0.2 200);
  --brand-foreground: oklch(0.98 0.01 80.72);
}
```

2. **Обновить Tailwind конфигурацию**:

```typescript
// tailwind.config.js
module.exports = {
  theme: {
    extend: {
      colors: {
        brand: "var(--brand)",
        "brand-foreground": "var(--brand-foreground)",
      },
    },
  },
};
```

## Тестирование

### Локальное тестирование

```bash
# Запустить в режиме разработки
pnpm dev

# Проверить компоненты
open http://localhost:3000/registry/button

# Проверить демо
open http://localhost:3000/demo/button

# Проверить JSON API
curl http://localhost:3000/r/button.json
```

### Тестирование сборки

```bash
# Собрать проект
pnpm build

# Проверить статические файлы
ls -la public/r/

# Запустить продакшн сервер
pnpm start
```

### Тестирование интеграции

```bash
# Тест Shadcn CLI
npx shadcn@latest add button --registry http://localhost:3000

# Тест MCP
npx shadcn@canary registry:mcp --registry http://localhost:3000/r/registry.json
```

## Отладка

### Общие проблемы

#### Компонент не отображается

1. Проверьте импорты в демо-файле
2. Убедитесь, что компонент добавлен в `index.tsx`
3. Проверьте консоль браузера на ошибки

#### JSON API не работает

1. Проверьте, что `registry:build` выполнился успешно
2. Убедитесь, что файлы созданы в `public/r/`
3. Проверьте права доступа к файлам

#### Стили не применяются

1. Проверьте CSS переменные в `globals.css`
2. Убедитесь, что Tailwind конфигурация корректна
3. Проверьте порядок импорта стилей

### Полезные команды

```bash
# Очистить кэш
rm -rf .next
pnpm dev

# Проверить типы
pnpm run lint

# Форматировать код
pnpm run lint:fix

# Пересобрать реестр
npm run registry:build
```

## Производительность

### Оптимизация сборки

1. **Используйте статическую генерацию**:

```typescript
export async function generateStaticParams() {
  return Object.keys(demos).map((name) => ({
    name,
  }));
}
```

2. **Минимизируйте размер JSON файлов**:

```typescript
// Удаляйте неиспользуемые импорты
// Используйте tree shaking
// Сжимайте код
```

3. **Оптимизируйте изображения**:

```typescript
import Image from "next/image";

export function OptimizedImage({ src, alt }) {
  return <Image src={src} alt={alt} width={500} height={300} priority />;
}
```

### Мониторинг

```typescript
// Добавить аналитику
import { Analytics } from "@vercel/analytics";

export function trackPageView(page: string) {
  Analytics.track("page_view", { page });
}
```

## Деплой

### Vercel

1. **Подключить репозиторий** к Vercel
2. **Настроить переменные окружения**:

```
REGISTRY_AUTH_TOKEN=your_token_here
NEXT_PUBLIC_REGISTRY_URL=https://your-domain.vercel.app
```

3. **Деплой** происходит автоматически при push в main

### Другие платформы

#### Netlify

```toml
# netlify.toml
[build]
  command = "npm run build"
  publish = ".next"

[[redirects]]
  from = "/r/*"
  to = "/r/:splat"
  status = 200
```

#### Docker

```dockerfile
FROM node:22-alpine

WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production

COPY . .
RUN npm run build

EXPOSE 3000
CMD ["npm", "start"]
```

## Лучшие практики

### Код

1. **Используйте TypeScript** для всех компонентов
2. **Документируйте пропсы** с JSDoc
3. **Следуйте принципам** чистого кода
4. **Тестируйте компоненты** перед добавлением в реестр

### Git

1. **Используйте осмысленные** commit сообщения
2. **Создавайте feature ветки** для новых компонентов
3. **Делайте code review** перед merge
4. **Ведите changelog** для изменений

### Производительность

1. **Минимизируйте bundle size**
2. **Используйте lazy loading** для тяжелых компонентов
3. **Оптимизируйте изображения**
4. **Мониторьте метрики** производительности
