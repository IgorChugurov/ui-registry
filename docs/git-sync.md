# Синхронизация с родительским репозиторием

## Обзор

Это руководство описывает полный процесс синхронизации вашего форка с оригинальным репозиторием `vercel/registry-starter`.

## Содержание

- [Настройка remotes](#настройка-remotes)
- [Получение обновлений](#получение-обновлений)
- [Отправка изменений](#отправка-изменений)
- [Разрешение конфликтов](#разрешение-конфликтов)
- [Полезные команды](#полезные-команды)

---

## Настройка remotes

### Проверка текущих remotes

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

### Если upstream не настроен

```bash
# Добавить upstream remote
git remote add upstream https://github.com/vercel/registry-starter.git

# Проверить
git remote -v
```

### Если нужно изменить upstream

```bash
# Удалить старый upstream
git remote remove upstream

# Добавить новый
git remote add upstream https://github.com/vercel/registry-starter.git
```

---

## Получение обновлений

### Быстрая синхронизация

```bash
# 1. Переключиться на main ветку
git checkout main

# 2. Получить последние изменения от upstream
git fetch upstream

# 3. Посмотреть, что изменилось
git --no-pager log HEAD..upstream/main --oneline

# 4. Слить изменения
git merge upstream/main

# 5. Отправить в ваш форк
git push origin main
```

### Детальная синхронизация

#### Шаг 1: Проверить текущее состояние

```bash
# Посмотреть статус
git status

# Посмотреть последние коммиты
git log --oneline -5

# Посмотреть ветки
git branch -a
```

#### Шаг 2: Получить обновления

```bash
# Получить все изменения от upstream
git fetch upstream

# Посмотреть различия
git diff HEAD upstream/main

# Посмотреть новые коммиты
git log HEAD..upstream/main --oneline
```

#### Шаг 3: Слить изменения

```bash
# Переключиться на main (если не на ней)
git checkout main

# Слить изменения от upstream
git merge upstream/main
```

#### Шаг 4: Отправить обновления

```bash
# Отправить в ваш форк
git push origin main
```

---

## Отправка изменений

### Создание Pull Request

#### Шаг 1: Создать feature ветку

```bash
# Создать новую ветку
git checkout -b feature/my-improvement

# Внести изменения
# ... редактирование файлов ...

# Коммит изменений
git add .
git commit -m "feat: add new component with improved styling"
```

#### Шаг 2: Отправить ветку

```bash
# Отправить feature ветку в ваш форк
git push origin feature/my-improvement
```

#### Шаг 3: Создать Pull Request

1. **Перейти на GitHub**: https://github.com/IgorChugurov/ui-registry
2. **Нажать "Compare & pull request"**
3. **Выбрать базовый репозиторий**: `vercel/registry-starter`
4. **Заполнить описание**:

   ```markdown
   ## Описание

   Добавлен новый компонент Alert с поддержкой различных вариантов.

   ## Изменения

   - Создан компонент Alert с вариантами: default, destructive, success, warning
   - Добавлены демо для всех вариантов
   - Обновлен registry.json

   ## Тестирование

   - [x] Локальное тестирование пройдено
   - [x] JSON API работает корректно
   - [x] Демо отображается правильно
   ```

#### Шаг 4: Отслеживание PR

```bash
# После создания PR, можно отслеживать статус
git fetch upstream
git log --oneline upstream/main..HEAD
```

### Обновление существующего PR

```bash
# Внести дополнительные изменения
# ... редактирование файлов ...

# Коммит изменений
git add .
git commit -m "fix: resolve styling issues in alert component"

# Отправить обновления
git push origin feature/my-improvement
```

---

## Разрешение конфликтов

### Автоматическое слияние

```bash
# Если конфликтов нет, слияние пройдет автоматически
git merge upstream/main
```

### Ручное разрешение конфликтов

#### Шаг 1: Обнаружение конфликтов

```bash
git merge upstream/main
# Git покажет файлы с конфликтами
```

**Пример вывода**:

```
Auto-merging src/components/ui/button.tsx
CONFLICT (content): Merge conflict in src/components/ui/button.tsx
Automatic merge failed; fix conflicts and then commit the result.
```

#### Шаг 2: Разрешение конфликтов

```bash
# Открыть файл с конфликтами
code src/components/ui/button.tsx
```

**Пример конфликта**:

```typescript
<<<<<<< HEAD
const buttonVariants = cva(
  "inline-flex items-center justify-center",
  {
    variants: {
      variant: {
        default: "bg-primary text-primary-foreground",
        // Ваши изменения
        success: "bg-green-500 text-white",
=======
const buttonVariants = cva(
  "inline-flex items-center justify-center",
  {
    variants: {
      variant: {
        default: "bg-primary text-primary-foreground",
        // Изменения из upstream
        outline: "border bg-background",
>>>>>>> upstream/main
      },
    },
  },
);
```

**Разрешение**:

```typescript
const buttonVariants = cva("inline-flex items-center justify-center", {
  variants: {
    variant: {
      default: "bg-primary text-primary-foreground",
      outline: "border bg-background",
      success: "bg-green-500 text-white",
    },
  },
});
```

#### Шаг 3: Завершение слияния

```bash
# Добавить разрешенные файлы
git add src/components/ui/button.tsx

# Завершить слияние
git commit -m "merge: resolve conflicts with upstream"

# Отправить изменения
git push origin main
```

### Отмена слияния

```bash
# Если нужно отменить слияние
git merge --abort

# Вернуться к состоянию до слияния
git reset --hard HEAD
```

---

## Полезные команды

### Проверка состояния

```bash
# Статус репозитория
git status

# История коммитов
git log --oneline -10

# Различия между ветками
git diff main upstream/main

# Посмотреть remotes
git remote -v

# Посмотреть ветки
git branch -a
```

### Работа с ветками

```bash
# Создать новую ветку
git checkout -b feature/new-feature

# Переключиться на ветку
git checkout main

# Удалить ветку
git branch -d feature/old-feature

# Удалить удаленную ветку
git push origin --delete feature/old-feature
```

### Работа с коммитами

```bash
# Посмотреть последний коммит
git show HEAD

# Изменить последний коммит
git commit --amend -m "New commit message"

# Отменить последний коммит
git reset --soft HEAD~1

# Отменить все изменения
git reset --hard HEAD
```

### Работа с удаленными репозиториями

```bash
# Получить изменения от всех remotes
git fetch --all

# Получить изменения от конкретного remote
git fetch upstream

# Посмотреть изменения
git log upstream/main..HEAD

# Синхронизировать с upstream
git pull upstream main
```

---

## Автоматизация

### Создание скрипта синхронизации

```bash
# Создать скрипт
touch scripts/sync-upstream.sh
chmod +x scripts/sync-upstream.sh
```

```bash
#!/bin/bash
# scripts/sync-upstream.sh

echo "🔄 Синхронизация с upstream..."

# Проверить, что мы на main ветке
if [ "$(git branch --show-current)" != "main" ]; then
    echo "❌ Переключитесь на main ветку"
    exit 1
fi

# Получить изменения
echo "📥 Получение изменений от upstream..."
git fetch upstream

# Посмотреть, что изменилось
echo "📋 Изменения:"
git log HEAD..upstream/main --oneline

# Спросить подтверждение
read -p "🤔 Продолжить слияние? (y/N): " -n 1 -r
echo
if [[ ! $REPLY =~ ^[Yy]$ ]]; then
    echo "❌ Отменено"
    exit 1
fi

# Слить изменения
echo "🔀 Слияние изменений..."
git merge upstream/main

# Отправить в ваш форк
echo "📤 Отправка в ваш форк..."
git push origin main

echo "✅ Синхронизация завершена!"
```

### Использование скрипта

```bash
# Запустить синхронизацию
./scripts/sync-upstream.sh
```

---

## Лучшие практики

### Регулярная синхронизация

- **Синхронизируйтесь еженедельно** с upstream
- **Проверяйте изменения** перед слиянием
- **Тестируйте после слияния**

### Работа с ветками

- **Создавайте feature ветки** для новых функций
- **Не работайте напрямую** в main ветке
- **Удаляйте старые ветки** после слияния

### Коммиты

- **Используйте осмысленные сообщения**
- **Группируйте связанные изменения**
- **Тестируйте перед коммитом**

### Pull Request'ы

- **Описывайте изменения** подробно
- **Включайте скриншоты** для UI изменений
- **Тестируйте перед отправкой**
- **Отвечайте на комментарии** быстро

---

## Troubleshooting

### Проблема: "Your branch is ahead of 'origin/main'"

```bash
# Это нормально, просто отправьте изменения
git push origin main
```

### Проблема: "Merge conflict"

```bash
# Разрешите конфликты вручную
# Затем добавьте файлы и завершите слияние
git add .
git commit -m "merge: resolve conflicts"
```

### Проблема: "Remote upstream not found"

```bash
# Добавьте upstream remote
git remote add upstream https://github.com/vercel/registry-starter.git
```

### Проблема: "Permission denied"

```bash
# Проверьте права доступа к репозиторию
# Убедитесь, что вы авторизованы в GitHub
git remote set-url origin https://github.com/IgorChugurov/ui-registry.git
```

---

## Полезные ссылки

- [GitHub Fork Guide](https://docs.github.com/en/get-started/quickstart/fork-a-repo)
- [Git Merge Conflicts](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/addressing-merge-conflicts)
- [Git Workflow](https://www.atlassian.com/git/tutorials/comparing-workflows)
- [Shadcn/ui Registry](https://ui.shadcn.com/docs/registry)
