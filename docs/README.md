# UI Registry Documentation

Добро пожаловать в документацию UI Registry - системы для создания и распространения дизайн-системы компонентов.

## Структура документации

- [Архитектура проекта](./architecture.md) - общая архитектура и принципы работы
- [Система регистров](./registry-system.md) - как работают регистры и их формирование
- [Система демо](./demo-system.md) - организация и создание демо-компонентов
- [Интеграция с AI](./ai-integration.md) - работа с v0.dev, Cursor, Windsurf
- [Разработка](./development.md) - руководство по разработке
- [Деплой](./deployment.md) - развертывание и настройка
- [Работа с компонентами](./component-workflow.md) - **изменение и создание компонентов**
- [Синхронизация с Git](./git-sync.md) - **работа с upstream репозиторием**
- [Быстрые команды](./quick-commands.md) - **все необходимые команды**

## Быстрый старт

1. **Просмотр компонентов**: https://ui-registry-lyart.vercel.app/registry/
2. **Демо компонентов**: https://ui-registry-lyart.vercel.app/demo/
3. **API документация**: https://ui-registry-lyart.vercel.app/r/

## Основные концепции

### Типы компонентов

- **UI Primitives** - базовые UI компоненты (button, input, card)
- **Components** - составные компоненты (hero, login, brand-header)
- **Blocks** - готовые блоки страниц (blank, dashboard, store)
- **Themes** - темы и CSS переменные

### Интеграция с AI

- **v0.dev**: Кнопка "Open in v0" для каждого компонента
- **Cursor/Windsurf**: MCP интеграция для автодополнения
- **API**: Прямой доступ к JSON файлам компонентов

## Полезные ссылки

- [Оригинальный проект](https://github.com/vercel/registry-starter)
- [Shadcn/ui документация](https://ui.shadcn.com/docs)
- [v0.dev](https://v0.dev)
- [MCP документация](https://modelcontextprotocol.io/)
