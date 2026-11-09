# CLAUDE.md

## Project Overview

`express-todo-api` — небольшой REST API для todo-приложений (демо/прототипы). Работает
как отдельный сервер (CLI) или как `express.Router`, подключаемый в чужое приложение.

- Стек: Node.js, Express 4, `body-parser`, `commander`, `uuid` v2, `defined`
- Данные хранятся **в памяти** (`util/store.js`), персистентности нет
- Ресурсы: `todos` (CRUD, смена статуса, назначение пользователя) и `users`
- Порт по умолчанию 4000 (`config.port` в package.json), префикс маршрутов `/api`
- Версия 1.1.1, лицензия ISC, автор Wes Todd

## Code Style

- Линтер/тесты: `happiness` (standard-подобный стиль), запуск через `npm test`
- ES5: `var`, `function`, без стрелочных функций, `const/let`, классов и async/await
- Отступы — **табы**, одинарные кавычки, точки с запятой в конце выражений
- CommonJS: `require` / `module.exports`
- Комментарии на английском, короткие, над логическим блоком (`// Create a new todo`)
- Один файл — один обработчик, экспортирует фабрику `function (options) { return function (req, res) {} }`

## Project Structure

```
index.js                 # Фабрика роутера: opts {prefix, bodyParser, store}
bin/express-todo-api     # CLI (commander): флаги --port, --prefix
handlers/
  todos/                 # index.js (роуты) + get/create/edit/status/assign/delete.js
  users/                 # index.js (роуты) + get/create/delete.js
util/
  store.js               # In-memory хранилище { todos: [], users: [] }
  status.js              # Допустимые статусы (active|complete) + valid()
  send-error.js          # Единый формат ошибок
  next-link.js           # Пагинация: ссылка на следующую страницу
  prev-link.js           # Пагинация: ссылка на предыдущую страницу
mokr/
  data/                  # Генерация случайных данных (faker)
  fixtures/              # mokr-фикстуры: POST на localhost:4000
```

## Key Rules

- Каждый обработчик берёт хранилище из `options.store`, **не** из `require('util/store')` —
  иначе сломается подмена `store` через опции
- Ошибки только через `sendError(res, code, msg)` → `{ error: { message } }`
- Статусы валидировать через `util/status.js`; новые статусы добавлять именно там
- Новый маршрут = новый файл в `handlers/<resource>/` + регистрация в `handlers/<resource>/index.js`
- Все пути строятся от `opts.prefix`; не хардкодить `/api`
- Список todos отдаёт `{ _links: {next, prev}, todos, total }`; ссылки строят `next-link`/`prev-link`
- Не мутировать объекты стора в ответах (см. клонирование при `withUser` в `todos/get.js`)
- `bodyParser` подключается внутри роутера по умолчанию; отключается `opts.bodyParser = false`
- Обновляя маршруты или опции, обновляйте таблицы в `README.md`

## Common Tasks

```bash
npm install                      # установка зависимостей
npm start                        # запуск сервера (bin/express-todo-api), порт 4000
./bin/express-todo-api -p 5000 -P /v1   # свой порт и префикс
npm test                         # линт через happiness (тестов нет)
npm run mocks                    # засеять данные через mokr (сервер должен работать)
npm run mocks-down               # удалить засеянные данные
```

Добавить эндпоинт для todos:
1. Создать `handlers/todos/<name>.js` по образцу `create.js`
2. Подключить в `handlers/todos/index.js`: `app.<method>('/path', require('./<name>')(opts))`
3. Описать маршрут в `README.md`
4. Прогнать `npm test`

Проверка вручную:
```bash
curl -X POST localhost:4000/api/todos -H 'Content-Type: application/json' -d '{"text":"demo"}'
curl 'localhost:4000/api/todos?status=active&limit=5&withUser=1'
```

## Gotchas

- **Автотестов нет**: `npm test` — только линт. Проверяйте изменения вручную через curl/mokr
- `prepublish` запускает `npm t`, а `postpublish` делает `git push && git push --tags` — осторожно с `npm publish`
- `handlers/todos/status.js` содержит баг: валидирует `req.params.status`, но
  читает/пишет `req.body.status` (в сообщении об ошибке и при присвоении) — PUT `/:id/status/:status` без тела не меняет статус
- `todos/get.js`: при `withUser` для todo с несуществующим пользователем делается
  `delete todo.assignedTo` прямо в сторе (мутация данных)
- `status.valid()` использует `this[...]`, поэтому вызывать только как `status.valid(x)`, не передавать функцию отдельно
- Пагинация: `limit` по умолчанию 5, `offset` 0; `parseInt` без проверки на NaN
- Данные теряются при перезапуске; `mokr` фикстуры жёстко бьют в `http://localhost:4000/api`
- В `mokr/.mokr` и `node_modules` игнорируются git (`.gitignore`)
- В `uuid` используется старый API v2 (`uuid.v4()`); не переходить на `import { v4 }` без обновления пакета
