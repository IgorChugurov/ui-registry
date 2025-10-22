# Руководство по работе с компонентами

## Обзор

Это руководство описывает полный флоу работы с компонентами в UI Registry: изменение существующих компонентов, создание новых примитивов и синхронизация с родительским репозиторием.

## Содержание

- [Изменение существующего компонента](#изменение-существующего-компонента)
- [Создание нового примитива](#создание-нового-примитива)
- [Синхронизация с родительским репозиторием](#синхронизация-с-родительским-репозиторием)
- [Отладка и проверка](#отладка-и-проверка)

---

## Изменение существующего компонента

### Шаг 1: Локальная разработка

#### 1.1. Внести изменения в реальный компонент

```bash
# Открыть файл компонента
code src/components/ui/button.tsx
```

**Пример изменения** - добавить новый вариант кнопки:

```typescript
// src/components/ui/button.tsx
const buttonVariants = cva(
  "inline-flex items-center justify-center gap-2 whitespace-nowrap rounded-md text-sm font-medium transition-all disabled:pointer-events-none disabled:opacity-50",
  {
    variants: {
      variant: {
        default:
          "bg-primary text-primary-foreground shadow-xs hover:bg-primary/90",
        destructive:
          "bg-destructive text-white shadow-xs hover:bg-destructive/90",
        outline:
          "border bg-background shadow-xs hover:bg-accent hover:text-accent-foreground",
        secondary:
          "bg-secondary text-secondary-foreground shadow-xs hover:bg-secondary/80",
        ghost: "hover:bg-accent hover:text-accent-foreground",
        link: "text-primary underline-offset-4 hover:underline",
        // ← ДОБАВИТЬ НОВЫЙ ВАРИАНТ
        success: "bg-green-500 text-white shadow-xs hover:bg-green-600",
      },
      // ... остальные варианты
    },
  }
);
```

#### 1.2. Обновить демо (если нужно)

```bash
# Открыть демо компонента
code src/app/demo/[name]/ui/button.tsx
```

**Добавить новый вариант в демо**:

```typescript
// src/app/demo/[name]/ui/button.tsx
import { Button } from "@/components/ui/button";

export const button = {
  name: "button",
  components: {
    Primary: <Button variant="default">Primary</Button>,
    Secondary: <Button variant="secondary">Secondary</Button>,
    Outline: <Button variant="outline">Outline</Button>,
    // ← ДОБАВИТЬ НОВЫЙ ВАРИАНТ
    Success: <Button variant="success">Success</Button>,
  },
};
```

#### 1.3. Проверить изменения локально

```bash
# Запустить в режиме разработки
npm run dev

# Открыть в браузере
open http://localhost:3000/demo/button
```

### Шаг 2: Пересборка реестра

```bash
# Пересобрать JSON файлы
npm run registry:build

# Проверить, что файлы созданы
ls -la public/r/button.json
```

### Шаг 3: Тестирование

```bash
# Проверить JSON API
curl http://localhost:3000/r/button.json

# Проверить демо-страницу
open http://localhost:3000/demo/button

# Проверить страницу реестра
open http://localhost:3000/registry/button
```

### Шаг 4: Коммит и пуш

```bash
# Добавить изменения
git add .

# Сделать коммит
git commit -m "feat: add success variant to button component"

# Отправить в ваш форк
git push origin main
```

---

## Создание нового примитива

### Шаг 1: Создание реального компонента

#### 1.1. Создать файл компонента

```bash
# Создать новый компонент
touch src/components/ui/alert.tsx
```

#### 1.2. Написать код компонента

```typescript
// src/components/ui/alert.tsx
import { cva, type VariantProps } from "class-variance-authority";
import { cn } from "@/lib/utils";

const alertVariants = cva(
  "relative w-full rounded-lg border p-4 [&>svg~*]:pl-7 [&>svg+div]:translate-y-[-3px] [&>svg]:absolute [&>svg]:left-4 [&>svg]:top-4 [&>svg]:text-foreground",
  {
    variants: {
      variant: {
        default: "bg-background text-foreground",
        destructive:
          "border-destructive/50 text-destructive dark:border-destructive [&>svg]:text-destructive",
        success:
          "border-green-500/50 text-green-700 bg-green-50 dark:border-green-500 dark:text-green-400 dark:bg-green-950",
        warning:
          "border-yellow-500/50 text-yellow-700 bg-yellow-50 dark:border-yellow-500 dark:text-yellow-400 dark:bg-yellow-950",
      },
    },
    defaultVariants: {
      variant: "default",
    },
  }
);

export interface AlertProps
  extends React.HTMLAttributes<HTMLDivElement>,
    VariantProps<typeof alertVariants> {}

const Alert = React.forwardRef<HTMLDivElement, AlertProps>(
  ({ className, variant, ...props }, ref) => (
    <div
      ref={ref}
      role="alert"
      className={cn(alertVariants({ variant }), className)}
      {...props}
    />
  )
);
Alert.displayName = "Alert";

const AlertTitle = React.forwardRef<
  HTMLParagraphElement,
  React.HTMLAttributes<HTMLHeadingElement>
>(({ className, ...props }, ref) => (
  <h5
    ref={ref}
    className={cn("mb-1 font-medium leading-none tracking-tight", className)}
    {...props}
  />
));
AlertTitle.displayName = "AlertTitle";

const AlertDescription = React.forwardRef<
  HTMLParagraphElement,
  React.HTMLAttributes<HTMLParagraphElement>
>(({ className, ...props }, ref) => (
  <div
    ref={ref}
    className={cn("text-sm [&_p]:leading-relaxed", className)}
    {...props}
  />
));
AlertDescription.displayName = "AlertDescription";

export { Alert, AlertTitle, AlertDescription };
```

### Шаг 2: Создание демо

#### 2.1. Создать файл демо

```bash
# Создать демо для нового компонента
touch src/app/demo/[name]/ui/alert.tsx
```

#### 2.2. Написать демо

```typescript
// src/app/demo/[name]/ui/alert.tsx
import { Alert, AlertDescription, AlertTitle } from "@/components/ui/alert";

export const alert = {
  name: "alert",
  components: {
    Default: (
      <Alert>
        <AlertTitle>Heads up!</AlertTitle>
        <AlertDescription>
          You can add components to your app using the cli.
        </AlertDescription>
      </Alert>
    ),
    Destructive: (
      <Alert variant="destructive">
        <AlertTitle>Error</AlertTitle>
        <AlertDescription>
          Your session has expired. Please log in again.
        </AlertDescription>
      </Alert>
    ),
    Success: (
      <Alert variant="success">
        <AlertTitle>Success!</AlertTitle>
        <AlertDescription>
          Your changes have been saved successfully.
        </AlertDescription>
      </Alert>
    ),
    Warning: (
      <Alert variant="warning">
        <AlertTitle>Warning</AlertTitle>
        <AlertDescription>
          Please review your changes before submitting.
        </AlertDescription>
      </Alert>
    ),
  },
};
```

### Шаг 3: Добавить в центральный экспорт

#### 3.1. Обновить index.tsx

```typescript
// src/app/demo/[name]/index.tsx
// ... existing imports
import { alert } from "@/app/demo/[name]/ui/alert";

export const demos: { [name: string]: Demo } = {
  // ... existing demos
  alert,
};
```

### Шаг 4: Добавить в registry.json

#### 4.1. Открыть registry.json

```bash
code registry.json
```

#### 4.2. Добавить новый компонент

```json
{
  "name": "alert",
  "type": "registry:ui",
  "title": "Alert",
  "description": "Displays a callout for user attention.",
  "registryDependencies": [
    "alert",
    "https://ui-registry-lyart.vercel.app/r/theme.json"
  ],
  "files": [
    {
      "path": "src/layouts/minimal-layout.tsx",
      "type": "registry:file",
      "target": "app/layout.tsx"
    }
  ]
}
```

### Шаг 5: Пересборка и тестирование

```bash
# Пересобрать реестр
npm run registry:build

# Запустить в режиме разработки
npm run dev

# Проверить демо
open http://localhost:3000/demo/alert

# Проверить JSON API
curl http://localhost:3000/r/alert.json

# Проверить страницу реестра
open http://localhost:3000/registry/alert
```

### Шаг 6: Коммит и пуш

```bash
# Добавить все изменения
git add .

# Сделать коммит
git commit -m "feat: add alert component with variants"

# Отправить в ваш форк
git push origin main
```

---

## Синхронизация с родительским репозиторием

### Получение обновлений от оригинального проекта

#### 1. Проверить текущие remotes

```bash
git remote -v
```

**Ожидаемый результат**:

```
origin    https://github.com/IgorChugurov/ui-registry.git (fetch)
origin    https://github.com/IgorChugurov/ui-registry.git (push)
upstream  https://github.com/vercel/registry-starter.git (fetch)
upstream  https://github.com/vercel/registry-starter.git (push)
```

#### 2. Получить обновления

```bash
# Получить последние изменения от upstream
git fetch upstream

# Посмотреть, что изменилось
git log HEAD..upstream/main --oneline
```

#### 3. Слить изменения

```bash
# Переключиться на main ветку
git checkout main

# Слить изменения от upstream
git merge upstream/main
```

#### 4. Разрешить конфликты (если есть)

```bash
# Если есть конфликты, отредактировать файлы
code src/components/ui/button.tsx

# После разрешения конфликтов
git add .
git commit -m "merge: resolve conflicts with upstream"
```

#### 5. Отправить обновления в ваш форк

```bash
# Отправить изменения в ваш репозиторий
git push origin main
```

### Отправка изменений в оригинальный проект

#### 1. Создать feature ветку

```bash
# Создать новую ветку для ваших изменений
git checkout -b feature/my-improvement

# Внести изменения
# ... редактирование файлов ...

# Коммит изменений
git add .
git commit -m "feat: improve button component"
```

#### 2. Отправить в ваш форк

```bash
# Отправить feature ветку
git push origin feature/my-improvement
```

#### 3. Создать Pull Request

1. **Перейти на GitHub**: https://github.com/IgorChugurov/ui-registry
2. **Нажать "Compare & pull request"**
3. **Выбрать базовый репозиторий**: `vercel/registry-starter`
4. **Заполнить описание** изменений
5. **Создать Pull Request**

---

## Отладка и проверка

### Проверка локальной разработки

```bash
# Запустить в режиме разработки
npm run dev

# Проверить все страницы
open http://localhost:3000/                    # Главная
open http://localhost:3000/registry/button      # Реестр
open http://localhost:3000/demo/button          # Демо
```

### Проверка JSON API

```bash
# Проверить JSON файлы
curl http://localhost:3000/r/button.json
curl http://localhost:3000/r/registry.json

# Проверить структуру
ls -la public/r/
```

### Проверка сборки

```bash
# Собрать проект
npm run build

# Проверить статические файлы
ls -la .next/static/

# Запустить продакшн сервер
npm start
```

### Проверка типов

```bash
# Проверить TypeScript
npm run lint

# Исправить ошибки
npm run lint:fix
```

### Проверка интеграции с AI

```bash
# Тест Shadcn CLI
npx shadcn@latest add button --registry http://localhost:3000

# Тест MCP
npx shadcn@canary registry:mcp --registry http://localhost:3000/r/registry.json
```

---

## Полезные команды

### Git команды

```bash
# Посмотреть статус
git status

# Посмотреть историю
git log --oneline -10

# Посмотреть различия
git diff

# Отменить изменения
git checkout -- filename

# Сбросить до последнего коммита
git reset --hard HEAD
```

### NPM команды

```bash
# Установить зависимости
npm install

# Запустить в режиме разработки
npm run dev

# Собрать проект
npm run build

# Запустить продакшн
npm start

# Проверить код
npm run lint

# Пересобрать реестр
npm run registry:build
```

### Отладка

```bash
# Очистить кэш
rm -rf .next node_modules
npm install

# Проверить переменные окружения
echo $NODE_ENV

# Проверить порт
lsof -i :3000
```

---

## Лучшие практики

### Коммиты

- **Используйте осмысленные сообщения**: `feat: add new component`, `fix: resolve button styling`
- **Группируйте связанные изменения** в один коммит
- **Тестируйте перед коммитом**

### Компоненты

- **Следуйте принципам** Shadcn/ui
- **Используйте TypeScript** для всех компонентов
- **Документируйте пропсы** с JSDoc
- **Создавайте демо** для всех вариантов

### Синхронизация

- **Регулярно синхронизируйтесь** с upstream
- **Создавайте feature ветки** для больших изменений
- **Тестируйте после слияния** с upstream
- **Документируйте изменения** в Pull Request'ах
