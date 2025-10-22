# Быстрые команды

## Обзор

Этот файл содержит все необходимые команды для работы с UI Registry проектом.

## Содержание

- [Основные команды](#основные-команды)
- [Работа с компонентами](#работа-с-компонентами)
- [Git команды](#git-команды)
- [Синхронизация с upstream](#синхронизация-с-upstream)
- [Отладка](#отладка)

---

## Основные команды

### Установка и запуск

```bash
# Установить зависимости
npm install

# Запустить в режиме разработки
npm run dev

# Собрать проект
npm run build

# Запустить продакшн сервер
npm start
```

### Пересборка реестра

```bash
# Пересобрать JSON файлы
npm run registry:build

# Проверить созданные файлы
ls -la public/r/
```

### Проверка кода

```bash
# Проверить код
npm run lint

# Исправить ошибки
npm run lint:fix
```

---

## Работа с компонентами

### Изменение существующего компонента

```bash
# 1. Открыть компонент
code src/components/ui/button.tsx

# 2. Внести изменения
# ... редактирование ...

# 3. Обновить демо (если нужно)
code src/app/demo/[name]/ui/button.tsx

# 4. Пересобрать реестр
npm run registry:build

# 5. Проверить изменения
npm run dev
open http://localhost:3000/demo/button
```

### Создание нового компонента

```bash
# 1. Создать реальный компонент
touch src/components/ui/alert.tsx

# 2. Создать демо
touch src/app/demo/[name]/ui/alert.tsx

# 3. Добавить в index.tsx
code src/app/demo/[name]/index.tsx

# 4. Добавить в registry.json
code registry.json

# 5. Пересобрать и проверить
npm run registry:build
npm run dev
```

### Проверка компонентов

```bash
# Проверить демо
open http://localhost:3000/demo/button

# Проверить реестр
open http://localhost:3000/registry/button

# Проверить JSON API
curl http://localhost:3000/r/button.json
```

---

## Git команды

### Основные операции

```bash
# Посмотреть статус
git status

# Добавить изменения
git add .

# Сделать коммит
git commit -m "feat: add new component"

# Отправить изменения
git push origin main
```

### Работа с ветками

```bash
# Создать новую ветку
git checkout -b feature/new-component

# Переключиться на ветку
git checkout main

# Посмотреть ветки
git branch -a

# Удалить ветку
git branch -d feature/old-branch
```

### История и различия

```bash
# Посмотреть историю
git log --oneline -10

# Посмотреть различия
git diff

# Посмотреть последний коммит
git show HEAD

# Отменить изменения
git checkout -- filename
```

---

## Синхронизация с upstream

### Получение обновлений

```bash
# 1. Получить изменения от upstream
git fetch upstream

# 2. Посмотреть, что изменилось
git log HEAD..upstream/main --oneline

# 3. Слить изменения
git checkout main
git merge upstream/main

# 4. Отправить в ваш форк
git push origin main
```

### Отправка изменений

```bash
# 1. Создать feature ветку
git checkout -b feature/my-improvement

# 2. Внести изменения и закоммитить
git add .
git commit -m "feat: improve component"

# 3. Отправить ветку
git push origin feature/my-improvement

# 4. Создать Pull Request на GitHub
```

### Разрешение конфликтов

```bash
# Если есть конфликты при слиянии
git merge upstream/main

# Разрешить конфликты в файлах
code src/components/ui/button.tsx

# Добавить разрешенные файлы
git add .

# Завершить слияние
git commit -m "merge: resolve conflicts"
```

---

## Отладка

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

### Очистка и переустановка

```bash
# Очистить кэш
rm -rf .next node_modules

# Переустановить зависимости
npm install

# Пересобрать реестр
npm run registry:build
```

---

## Полезные команды

### Проверка портов

```bash
# Проверить, что порт 3000 свободен
lsof -i :3000

# Освободить порт
kill -9 $(lsof -t -i:3000)
```

### Проверка переменных окружения

```bash
# Проверить переменные
echo $NODE_ENV
echo $NEXT_PUBLIC_REGISTRY_URL

# Установить переменные
export NODE_ENV=development
export NEXT_PUBLIC_REGISTRY_URL=http://localhost:3000
```

### Работа с файлами

```bash
# Найти файлы
find . -name "*.tsx" -type f

# Найти текст в файлах
grep -r "Button" src/

# Посмотреть размер файлов
du -sh public/r/*
```

---

## Интеграция с AI

### Тест Shadcn CLI

```bash
# Тест локального реестра
npx shadcn@latest add button --registry http://localhost:3000

# Тест MCP
npx shadcn@canary registry:mcp --registry http://localhost:3000/r/registry.json
```

### Тест v0.dev

```bash
# Проверить URL для v0
echo "https://v0.dev/chat/api/open?url=http://localhost:3000/r/button.json"
```

---

## Быстрые скрипты

### Создание скрипта синхронизации

```bash
# Создать скрипт
cat > scripts/sync.sh << 'EOF'
#!/bin/bash
echo "🔄 Синхронизация с upstream..."
git fetch upstream
git checkout main
git merge upstream/main
git push origin main
echo "✅ Готово!"
EOF

chmod +x scripts/sync.sh
```

### Использование скрипта

```bash
# Запустить синхронизацию
./scripts/sync.sh
```

### Создание скрипта для нового компонента

```bash
# Создать скрипт
cat > scripts/new-component.sh << 'EOF'
#!/bin/bash
COMPONENT_NAME=$1

if [ -z "$COMPONENT_NAME" ]; then
    echo "❌ Укажите имя компонента: ./scripts/new-component.sh alert"
    exit 1
fi

echo "🔧 Создание компонента: $COMPONENT_NAME"

# Создать файлы
touch "src/components/ui/$COMPONENT_NAME.tsx"
touch "src/app/demo/[name]/ui/$COMPONENT_NAME.tsx"

echo "✅ Файлы созданы:"
echo "  - src/components/ui/$COMPONENT_NAME.tsx"
echo "  - src/app/demo/[name]/ui/$COMPONENT_NAME.tsx"
echo ""
echo "📝 Не забудьте:"
echo "  1. Написать код компонента"
echo "  2. Создать демо"
echo "  3. Добавить в index.tsx"
echo "  4. Добавить в registry.json"
echo "  5. Запустить npm run registry:build"
EOF

chmod +x scripts/new-component.sh
```

### Использование скрипта

```bash
# Создать новый компонент
./scripts/new-component.sh alert
```

---

## Алиасы для терминала

### Добавить в .bashrc или .zshrc

```bash
# UI Registry алиасы
alias ui-dev="npm run dev"
alias ui-build="npm run build"
alias ui-registry="npm run registry:build"
alias ui-sync="git fetch upstream && git checkout main && git merge upstream/main && git push origin main"
alias ui-status="git status"
alias ui-log="git log --oneline -10"
```

### Использование алиасов

```bash
# Запустить в режиме разработки
ui-dev

# Пересобрать реестр
ui-registry

# Синхронизироваться с upstream
ui-sync

# Посмотреть статус
ui-status
```

---

## Troubleshooting

### Проблема: "Port 3000 is already in use"

```bash
# Найти процесс
lsof -i :3000

# Убить процесс
kill -9 $(lsof -t -i:3000)

# Или использовать другой порт
npm run dev -- --port 3001
```

### Проблема: "Module not found"

```bash
# Очистить кэш
rm -rf .next node_modules

# Переустановить
npm install

# Пересобрать
npm run build
```

### Проблема: "JSON files not generated"

```bash
# Проверить registry.json
cat registry.json

# Пересобрать реестр
npm run registry:build

# Проверить файлы
ls -la public/r/
```

### Проблема: "Git conflicts"

```bash
# Посмотреть конфликты
git status

# Разрешить конфликты
git add .
git commit -m "merge: resolve conflicts"

# Или отменить слияние
git merge --abort
```

---

## Полезные ссылки

- [Next.js Documentation](https://nextjs.org/docs)
- [Shadcn/ui Documentation](https://ui.shadcn.com/docs)
- [Git Documentation](https://git-scm.com/docs)
- [Vercel Documentation](https://vercel.com/docs)
