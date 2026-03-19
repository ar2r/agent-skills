# Claude Skills

Плагины и skills для Claude Code.

## Установка

```
/plugins add https://github.com/ar2r/claude-skills
```

## Обновление

```
/plugins update dev-review-pro
```

Если плагин уже был установлен под старым именем `fix-bug-pro`, переустанови его из репозитория после обновления.

## Доступные плагины

| Плагин | Описание |
|--------|----------|
| **dev-review-pro** | Набор workflows для исправления багов и подготовки описаний Merge Request в GitLab |

## Skills в `dev-review-pro`

| Skill | Описание |
|-------|----------|
| **fix-bug-pro** | Профессиональный workflow для расследования багов, безопасного фикса и регрессионного покрытия |
| **mr-desc** | Генерация структурированного описания MR/PR на русском языке по diff и коммитам с опцией публикации через `glab` |

## Триггеры

### `fix-bug-pro`

Автоматически активируется на фразы:

```
"fix this bug", "debug", "failing test", "not working", "crashes when"
"почини баг", "исправь ошибку", "найди причину"
```

### `mr-desc`

Активируется, когда пользователь:

```
передаёт ссылку на GitLab MR
просит написать или обновить описание PR/MR
просит подготовить текст изменений для ревью
упоминает "changelog ветки" или "текст для MR/PR"
```

## Структура

```
claude-skills/
├── .claude-plugin/marketplace.json
└── plugins/dev-review-pro/
    ├── README.md
    ├── .claude-plugin/plugin.json
    └── skills/
        ├── fix-bug-pro/
        │   ├── SKILL.md
        │   └── evals/evals.json
        └── mr-desc/
            ├── SKILL.md
            └── evals/evals.json
```

MIT License · Artur Khasanov
