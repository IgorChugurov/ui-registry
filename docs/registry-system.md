# Система регистров

## Обзор

Система регистров - это ядро UI Registry, которое позволяет создавать, каталогизировать и распространять UI компоненты через JSON API.

## Основные компоненты

### 1. registry.json

**Центральный файл конфигурации** в корне проекта:

```json
{
  "$schema": "https://ui.shadcn.com/schema/registry.json",
  "name": "Registry Starter",
  "homepage": "https://ui-registry-lyart.vercel.app",
  "items": [
    {
      "name": "theme",
      "type": "registry:theme",
      "title": "Nature Theme",
      "description": "A nature theme with greens and browns",
      "cssVars": {
        "light": {
          /* CSS переменные для светлой темы */
        },
        "dark": {
          /* CSS переменные для темной темы */
        }
      }
    }
  ]
}
```

### 2. Типы компонентов

#### registry:theme

Темы и CSS переменные:

```json
{
  "name": "theme",
  "type": "registry:theme",
  "title": "Nature Theme",
  "cssVars": {
    "light": {
      "background": "oklch(0.97 0.01 80.72)",
      "foreground": "oklch(0.3 0.04 30.2)"
    }
  }
}
```

#### registry:ui

UI примитивы (button, input, card):

```json
{
  "name": "button",
  "type": "registry:ui",
  "title": "Button",
  "description": "Allows users to take actions",
  "registryDependencies": ["button", "theme"]
}
```

#### registry:component

Составные компоненты (hero, login, brand-header):

```json
{
  "name": "hero",
  "type": "registry:component",
  "title": "Hero",
  "description": "Attention-grabbing section",
  "registryDependencies": ["badge", "button", "theme"]
}
```

#### registry:block

Готовые блоки страниц (blank, dashboard, store):

```json
{
  "name": "dashboard",
  "type": "registry:block",
  "title": "Dashboard",
  "description": "A dashboard application",
  "registryDependencies": ["sonner", "brand-header", "theme"]
}
```

## Генерация JSON API

### Процесс сборки

```bash
npm run registry:build  # npx shadcn@latest build
```

**Что происходит**:

1. Читается `registry.json`
2. Для каждого компонента создается JSON файл в `/public/r/`
3. В JSON включается код компонента и метаданные

### Структура сгенерированного JSON

**Пример**: `/public/r/button.json`

```json
{
  "$schema": "https://ui.shadcn.com/schema/registry-item.json",
  "name": "button",
  "type": "registry:ui",
  "title": "Button",
  "description": "Allows users to take actions",
  "registryDependencies": [
    "button",
    "https://ui-registry-lyart.vercel.app/r/theme.json"
  ],
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

## Зависимости

### registryDependencies

Компоненты могут зависеть от других компонентов:

```json
{
  "registryDependencies": [
    "button", // локальная зависимость
    "https://ui-registry-lyart.vercel.app/r/theme.json" // внешняя зависимость
  ]
}
```

### Типы зависимостей

1. **Локальные** - ссылки на другие компоненты в том же реестре
2. **Внешние** - полные URL на JSON файлы других реестров
3. **NPM пакеты** - зависимости из package.json

## Файловая система

### Структура файлов

Каждый компонент может включать:

```json
{
  "files": [
    {
      "path": "src/components/button.tsx",
      "content": "/* код компонента */",
      "type": "registry:component",
      "target": "components/ui/button.tsx"
    },
    {
      "path": "src/layouts/minimal-layout.tsx",
      "content": "/* код лейаута */",
      "type": "registry:file",
      "target": "app/layout.tsx"
    }
  ]
}
```

### Типы файлов

- **registry:component** - React компонент
- **registry:file** - обычный файл
- **registry:style** - CSS файл
- **registry:page** - страница Next.js

## API эндпоинты

### Получение компонента

```
GET /r/button.json
```

**Ответ**:

```json
{
  "name": "button",
  "type": "registry:ui",
  "files": [
    /* файлы компонента */
  ]
}
```

### Получение всего реестра

```
GET /r/registry.json
```

**Ответ**: полный `registry.json` файл.

## Интеграция с Shadcn CLI

### Установка компонента

```bash
npx shadcn@latest add button --registry https://ui-registry-lyart.vercel.app
```

### Настройка реестра

```bash
npx shadcn@latest init --registry https://ui-registry-lyart.vercel.app
```

## Кастомизация

### Добавление нового компонента

1. **Создать компонент** в `src/components/ui/`
2. **Добавить в registry.json**:

```json
{
  "name": "my-component",
  "type": "registry:ui",
  "title": "My Component",
  "description": "Custom component",
  "registryDependencies": ["theme"]
}
```

3. **Создать демо** в `src/app/demo/[name]/ui/`
4. **Запустить сборку**: `npm run registry:build`

### Изменение темы

1. **Отредактировать CSS переменные** в `src/app/globals.css`
2. **Обновить registry.json**:

```json
{
  "name": "theme",
  "cssVars": {
    "light": {
      /* новые цвета */
    },
    "dark": {
      /* новые цвета */
    }
  }
}
```

3. **Пересобрать**: `npm run registry:build`

## Лучшие практики

### Организация компонентов

- **UI Primitives** - базовые, переиспользуемые компоненты
- **Components** - составные, специфичные для домена
- **Blocks** - готовые страницы и лейауты

### Зависимости

- Минимизируйте внешние зависимости
- Используйте локальные зависимости когда возможно
- Документируйте все зависимости

### Версионирование

- Используйте семантическое версионирование
- Тестируйте изменения перед публикацией
- Ведите changelog

## Отладка

### Проверка JSON

```bash
curl https://ui-registry-lyart.vercel.app/r/button.json
```

### Локальная разработка

```bash
npm run dev
# Проверить http://localhost:3000/r/button.json
```

### Логи сборки

```bash
npm run registry:build --verbose
```
