# Система демо

## Обзор

Система демо позволяет создавать интерактивные примеры использования компонентов, которые отображаются на демо-страницах и помогают разработчикам понять, как использовать каждый компонент.

## Структура демо

### Организация файлов

```
src/app/demo/[name]/
├── index.tsx              # Центральный экспорт всех демо
├── page.tsx               # Рендер демо-страницы
├── renderer.tsx           # Компонент для рендера демо
├── blocks/                # Демо блоков
│   ├── blank.tsx
│   ├── dashboard.tsx
│   └── store.tsx
├── components/            # Демо компонентов
│   ├── hero.tsx
│   ├── login.tsx
│   ├── brand-header.tsx
│   └── brand-sidebar.tsx
└── ui/                    # Демо UI примитивов
    ├── button.tsx
    ├── input.tsx
    ├── card.tsx
    └── ...
```

## Создание демо

### Структура демо-файла

Каждый демо-файл экспортирует объект с компонентами:

```typescript
// src/app/demo/[name]/ui/button.tsx
import { Button } from "@/components/ui/button";

export const button = {
  name: "button",
  components: {
    Primary: <Button variant="default">Primary</Button>,
    Secondary: <Button variant="secondary">Secondary</Button>,
    Outline: <Button variant="outline">Outline</Button>,
    Destructive: <Button variant="destructive">Destructive</Button>,
    Ghost: <Button variant="ghost">Ghost</Button>,
    Link: <Button variant="link">Link</Button>,
  },
};
```

### Типы демо

#### UI Primitives

Базовые компоненты с разными вариантами:

```typescript
export const input = {
  name: "input",
  components: {
    Default: <Input placeholder="Enter your email" />,
    WithLabel: (
      <div className="space-y-2">
        <Label htmlFor="email">Email</Label>
        <Input id="email" placeholder="Enter your email" />
      </div>
    ),
    Disabled: <Input disabled placeholder="Disabled input" />,
  },
};
```

#### Components

Составные компоненты с пропсами:

```typescript
export const hero = {
  name: "hero",
  components: {
    Default: (
      <Hero
        title="Build a Registry"
        description="This starter helps you create a registry"
        buttonText="Learn more"
        buttonLink="#sale"
        backgroundImage="/assets/hero.png"
      />
    ),
    Minimal: (
      <Hero
        title="Simple Hero"
        description="A minimal hero section"
        buttonText="Get Started"
      />
    ),
  },
};
```

#### Blocks

Готовые блоки страниц:

```typescript
export const dashboard = {
  name: "dashboard",
  components: {
    Default: <DashboardPage />,
  },
};
```

## Центральный экспорт

### index.tsx

Все демо экспортируются через центральный файл:

```typescript
// src/app/demo/[name]/index.tsx
import type { ReactElement, ReactNode } from "react";

// blocks
import { blank } from "@/app/demo/[name]/blocks/blank";
import { dashboard } from "@/app/demo/[name]/blocks/dashboard";
import { store } from "@/app/demo/[name]/blocks/store";

// components
import { brandHeader } from "@/app/demo/[name]/components/brand-header";
import { hero } from "@/app/demo/[name]/components/hero";
import { login } from "@/app/demo/[name]/components/login";

// ui
import { accordion } from "@/app/demo/[name]/ui/accordion";
import { button } from "@/app/demo/[name]/ui/button";
import { input } from "@/app/demo/[name]/ui/input";

interface Demo {
  name: string;
  components?: {
    [name: string]: ReactNode | ReactElement;
  };
}

export const demos: { [name: string]: Demo } = {
  // blocks
  blank,
  store,
  dashboard,

  // components
  hero,
  login,
  "brand-header": brandHeader,

  // ui
  accordion,
  button,
  input,
  // ... остальные компоненты
};
```

## Рендеринг демо

### page.tsx

Демо-страница рендерит все варианты компонента:

```typescript
// src/app/demo/[name]/page.tsx
import { notFound } from "next/navigation";
import { demos } from "@/app/demo/[name]/index";
import { Renderer } from "@/app/demo/[name]/renderer";
import { getRegistryItem } from "@/lib/registry";

export async function generateStaticParams() {
  return Object.keys(demos).map((name) => ({
    name,
  }));
}

export default async function DemoPage({
  params,
}: {
  params: Promise<{ name: string }>;
}) {
  const { name } = await params;
  const component = getRegistryItem(name);

  if (!component) {
    notFound();
  }

  const { components } = demos[name];

  return (
    <div className="flex h-[100vh] w-full flex-col gap-4 bg-card">
      {components &&
        Object.entries(components).map(([key, node]) => (
          <div className="relative w-full" key={key}>
            <Renderer>{node}</Renderer>
          </div>
        ))}
    </div>
  );
}
```

### renderer.tsx

Компонент для рендера демо:

```typescript
// src/app/demo/[name]/renderer.tsx
import { ReactNode } from "react";

interface RendererProps {
  children: ReactNode;
}

export function Renderer({ children }: RendererProps) {
  return <>{children}</>;
}
```

## Маршрутизация

### URL структура

- `/demo/button` - демо кнопки
- `/demo/hero` - демо hero компонента
- `/demo/dashboard` - демо dashboard блока

### Статическая генерация

```typescript
export async function generateStaticParams() {
  return Object.keys(demos).map((name) => ({
    name,
  }));
}
```

Все демо-страницы генерируются статически во время сборки.

## Лучшие практики

### Создание демо

1. **Показывайте разные варианты** использования компонента
2. **Используйте осмысленные названия** для вариантов
3. **Включайте edge cases** (disabled, error states)
4. **Документируйте пропсы** в комментариях

### Пример хорошего демо

```typescript
export const card = {
  name: "card",
  components: {
    Default: (
      <Card>
        <CardHeader>
          <CardTitle>Card Title</CardTitle>
          <CardDescription>Card description</CardDescription>
        </CardHeader>
        <CardContent>
          <p>Card content</p>
        </CardContent>
        <CardFooter>
          <Button>Action</Button>
        </CardFooter>
      </Card>
    ),
    Simple: (
      <Card>
        <CardContent>
          <p>Simple card content</p>
        </CardContent>
      </Card>
    ),
    WithImage: (
      <Card>
        <CardHeader>
          <img src="/placeholder.jpg" alt="Card image" />
        </CardHeader>
        <CardContent>
          <p>Card with image</p>
        </CardContent>
      </Card>
    ),
  },
};
```

### Избегайте

- Слишком много вариантов (больше 5-6)
- Неосмысленные названия
- Отсутствие интерактивности
- Сложные зависимости между демо

## Отладка демо

### Проверка рендера

```bash
npm run dev
# Открыть http://localhost:3000/demo/button
```

### Проверка экспорта

```typescript
// В консоли браузера
console.log(demos.button);
```

### Логи ошибок

Проверьте консоль браузера на ошибки рендера компонентов.

## Интеграция с реестром

### Связь с registry.json

Каждое демо должно соответствовать записи в `registry.json`:

```json
{
  "name": "button",
  "type": "registry:ui",
  "title": "Button"
}
```

### Обновление демо

При изменении компонента обновите соответствующее демо:

1. Измените компонент в `src/components/ui/`
2. Обновите демо в `src/app/demo/[name]/ui/`
3. Проверьте рендер: `npm run dev`

## Производительность

### Оптимизация рендера

- Используйте `React.memo` для тяжелых компонентов
- Избегайте сложных вычислений в демо
- Минимизируйте количество вариантов

### Ленивая загрузка

```typescript
const LazyComponent = lazy(() => import("./heavy-component"));

export const heavyDemo = {
  name: "heavy",
  components: {
    Default: (
      <Suspense fallback={<div>Loading...</div>}>
        <LazyComponent />
      </Suspense>
    ),
  },
};
```
