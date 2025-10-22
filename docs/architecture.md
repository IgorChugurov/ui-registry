# Архитектура проекта

## Обзор

UI Registry построен на Next.js 15 с использованием App Router и представляет собой систему для создания, каталогизации и распространения UI компонентов.

## Технологический стек

- **Next.js 15.5.2** - React фреймворк с App Router
- **React 19.1.0** - UI библиотека
- **TypeScript 5.4.5** - типизация
- **Tailwind CSS 4.1.11** - стилизация
- **Shadcn/ui** - компонентная библиотека
- **Radix UI** - примитивы для доступности
- **Vercel** - хостинг и деплой

## Структура проекта

```
src/
├── app/                          # Next.js App Router
│   ├── (registry)/              # Группа маршрутов реестра
│   │   ├── layout.tsx           # Лейаут для реестра
│   │   ├── page.tsx             # Главная страница
│   │   └── registry/[name]/     # Страницы компонентов
│   ├── demo/[name]/             # Демо-страницы
│   │   ├── blocks/              # Демо блоков
│   │   ├── components/          # Демо компонентов
│   │   ├── ui/                  # Демо UI примитивов
│   │   ├── index.tsx            # Экспорт всех демо
│   │   ├── page.tsx              # Рендер демо
│   │   └── renderer.tsx          # Компонент рендера
│   ├── globals.css              # Глобальные стили
│   └── layout.tsx               # Корневой лейаут
├── components/                   # React компоненты
│   ├── ui/                      # Shadcn/ui компоненты
│   ├── registry/                # Компоненты реестра
│   └── [brand-components]       # Брендовые компоненты
├── lib/                         # Утилиты и логика
│   ├── registry.ts              # Работа с реестром
│   ├── utils.ts                 # Общие утилиты
│   └── products.ts              # Данные продуктов
└── layouts/                     # Лейауты для v0
    ├── minimal-layout.tsx
    └── shell-layout.tsx
```

## Ключевые принципы

### 1. Компонентная архитектура

Все компоненты организованы по типам:

- **UI Primitives** - базовые компоненты (button, input)
- **Components** - составные компоненты (hero, login)
- **Blocks** - готовые блоки страниц (dashboard, store)

### 2. Система регистров

**registry.json** - центральный файл конфигурации:

```json
{
  "name": "Registry Starter",
  "homepage": "https://ui-registry-lyart.vercel.app",
  "items": [
    {
      "name": "button",
      "type": "registry:ui",
      "title": "Button",
      "description": "Allows users to take actions",
      "registryDependencies": ["button", "theme"]
    }
  ]
}
```

### 3. Генерация JSON API

**Процесс сборки**:

```bash
npm run registry:build  # npx shadcn@latest build
```

Создает JSON файлы в `/public/r/` для каждого компонента.

### 4. Демо-система

Каждый компонент имеет демо-версию:

```typescript
export const button = {
  name: "button",
  components: {
    Primary: <Button variant="default">Primary</Button>,
    Secondary: <Button variant="secondary">Secondary</Button>,
  },
};
```

## Маршрутизация

### App Router структура

- `/` - главная страница реестра
- `/registry/[name]` - страница компонента
- `/demo/[name]` - демо компонента
- `/r/[name].json` - JSON API компонента

### Динамическая генерация

```typescript
export async function generateStaticParams() {
  return Object.keys(demos).map((name) => ({
    name,
  }));
}
```

## Стилизация

### CSS переменные

Используется OKLCH цветовое пространство:

```css
:root {
  --background: oklch(0.97 0.01 80.72);
  --foreground: oklch(0.3 0.04 30.2);
  --primary: oklch(0.52 0.13 144.17);
}
```

### Tailwind CSS 4

- **CSS-in-JS** через CSS переменные
- **Dark mode** через `.dark` класс
- **Responsive design** через утилиты Tailwind

## Производительность

### Оптимизации

- **Static Generation** - все страницы генерируются статически
- **Turbopack** - быстрая разработка
- **Tree Shaking** - оптимизация бандла
- **CDN** - статические файлы через Vercel

### Кэширование

- **JSON файлы** кэшируются на CDN
- **Компоненты** кэшируются в браузере
- **Изображения** оптимизируются автоматически

## Безопасность

### Аутентификация

Для защиты JSON API:

```typescript
export function middleware(request: NextRequest) {
  const token = request.nextUrl.searchParams.get("token");
  if (token !== process.env.REGISTRY_AUTH_TOKEN) {
    return new NextResponse("Unauthorized", { status: 401 });
  }
  return NextResponse.next();
}
```

### CORS

Настроен для работы с внешними AI-инструментами.

## Масштабируемость

### Добавление компонентов

1. Создать компонент в `src/components/`
2. Добавить в `registry.json`
3. Создать демо в `src/app/demo/[name]/`
4. Запустить `npm run registry:build`

### Кастомизация

- **Темы** - изменение CSS переменных
- **Компоненты** - добавление новых компонентов
- **Блоки** - создание новых блоков страниц
