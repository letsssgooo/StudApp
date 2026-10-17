# Пользовательские сервисы StudApp

Описана целевая архитектура пользовательских возможностей: границы ответственности, данные, взаимодействия и логика экранов.

Состав MVP — согласно [PRD][prd] и [OnePager][onepager], бизнес-правила — согласно [SRS][srs] и [User Stories][us], приёмочные проверки — согласно [RTM][rtm].

## Стек backend

- **Go** — backend; **PostgreSQL** — база данных.
- **chi** — HTTP-маршруты и middleware.
- **golang-migrate** — SQL-миграции `.up.sql` / `.down.sql`.
- **log/slog** — структурированные логи.
- **Docker + Docker Compose** — локальный запуск.
- **testing + httptest** — тесты; PostgreSQL — интеграционные проверки.
- **OpenAPI** — спецификация API в `api/`.

## Модульный монолит

Backend запускается как одно приложение на Go. Каждый сервис ниже задаёт границу бизнес-логики: владеет своими данными, правилами и прикладными операциями и предоставляет контракты другим сервисам.


## Каталог сервисов

| Сервис | Задачи пользователя | Связанные сервисы |
|---|---|---|
| `account` — [Кабинет и учебный контекст](services/account.md) | Вход, выбор заведения, доступные возможности, выход | `administration` |
| `schedule` — [Расписание](services/schedule.md) | Неделя, занятие, связанные материалы и задания; управление занятиями | `administration`, `materials`, `assignments`, `notifications` |
| `materials` — [Учебные материалы](services/materials.md) | Открытие материала; подготовка и публикация | `administration`, `schedule` |
| `assignments` — [Задания и дедлайны](services/assignments-deadlines.md) | Условие задания, публикация, сроки, состав команд | `administration`, `submissions`, `notifications` |
| `submissions` — [Сдача работ](services/submissions.md) | Подготовка и сдача ответа, замена и отзыв, командная работа | `administration`, `assignments`, `review` |
| `review` — [Проверка работ](services/review.md) | Проверка ответа, оценка и комментарий, возврат на доработку | `administration`, `submissions`, `results`, `notifications` |
| `results` — [Результаты и приватность](services/results.md) | Опубликованные оценки, итог дисциплины, видимость данных | `administration`, `review` |
| `notifications` — [Уведомления и напоминания](services/notifications.md) | Учебные события, каналы доставки, личные напоминания | `administration`, `assignments`, `schedule`, `review` |
| `administration` — [Учебное администрирование](services/administration.md) | Заведения, дисциплины, группы, участники, роли и настройки | `account`, `schedule`, `assignments` |

## C4: контекст системы

Роли и внешняя email-доставка находятся за границей StudApp. Конкретные полномочия ролей определяются требованиями и учебным контекстом.

```mermaid
%%{init: {"theme": "default", "look": "classic", "layout": "dagre", "flowchart": {"htmlLabels": true, "wrappingWidth": 260, "nodeSpacing": 40, "rankSpacing": 60}}}%%
flowchart TB
    student(["<b>Студент</b><br/>[Person]<br/>Находит материалы, сдаёт<br/>работы, получает результат"]):::person
    teacher(["<b>Преподаватель</b><br/>[Person]<br/>Публикует, задаёт сроки,<br/>проверяет"]):::person
    assistant(["<b>Ассистент</b><br/>[Person]<br/>Выполняет разрешённые учебные<br/>операции"]):::person
    admin(["<b>Учебная администрация</b><br/>[Person]<br/>Управляет контекстом и<br/>расписанием"]):::person
    studapp["<b>StudApp</b><br/>[Software System]<br/>Единый учебный процесс<br/>нескольких заведений"]:::system
    email["<b>Email-доставка</b><br/>[External Software System]<br/>Доставка уведомлений по email"]:::external
    student -->|"Учится в назначенном контексте"| studapp
    teacher -->|"Организует учебную работу"| studapp
    assistant -->|"Работает в пределах полномочий"| studapp
    admin -->|"Администрирует своё заведение"| studapp
    studapp -->|"Передаёт разрешённые<br/>уведомления"| email

    classDef person fill:#173f68,color:#fff,stroke:#0d2947
    classDef system fill:#1964a3,color:#fff,stroke:#124b7a
    classDef external fill:#eceff2,color:#333,stroke:#777
```

## C4: контейнеры

Web-приложение обращается к одному Go-backend по HTTP API. Backend исполняет сценарии и работает с хранилищем учебных записей и внешней доставкой email. Контейнер здесь означает приложение или хранилище в модели C4. Учебные записи хранятся в PostgreSQL.

```mermaid
%%{init: {"theme": "default", "look": "classic", "layout": "dagre", "flowchart": {"htmlLabels": true, "wrappingWidth": 260, "nodeSpacing": 40, "rankSpacing": 60}}}%%
flowchart LR
    user(["<b>Пользователь</b><br/>[Person]<br/>Студент или сотрудник с<br/>назначенными правами"]):::person
    subgraph system["StudApp · Software System"]
        web["<b>Web-приложение</b><br/>[Container · Web]<br/>Пользовательские действия и<br/>результаты"]:::container
        backend["<b>Backend</b><br/>[Container · Go, модульный монолит]<br/>Учебные сценарии и контроль<br/>доступа"]:::container
        data[("<b>Учебные записи</b><br/>[Container · PostgreSQL]<br/>Контексты, занятия, задания,<br/>сдачи и результаты")]:::container
    end
    email["<b>Email-доставка</b><br/>[External Software System]<br/>Доставка уведомлений по email"]:::external
    user -->|"Выполняет учебные действия"| web
    web -->|"Передаёт запросы и получает<br/>результаты<br/>[HTTP API]"| backend
    backend -->|"Читает и сохраняет данные"| data
    backend -->|"Отправляет уведомления<br/>включённого канала"| email

    classDef person fill:#173f68,color:#fff,stroke:#0d2947
    classDef container fill:#d7e8f7,color:#15334f,stroke:#427fae
    classDef external fill:#eceff2,color:#333,stroke:#777
```

## Примерный план реализации

1. **Подготовить основу:** Go-модуль, HTTP-сервер, конфигурацию, логи, PostgreSQL, миграции и запуск через Docker Compose.
2. **Реализовать `account` и `administration`:** учётные записи, вход, учебную структуру, роли, выбор заведения и проверку доступа.
3. **Реализовать `schedule` и `materials`:** управление занятиями, публикацию материалов и доступ к файлам.
4. **Реализовать `assignments`:** задания, команды, дедлайны и публикацию по времени.
5. **Реализовать `submissions`:** сдачу, замену и отзыв ответов, командную работу.
6. **Реализовать `review` и `results`:** проверку, оценки, доработку, итог дисциплины и приватность.
7. **Реализовать `notifications`:** уведомления внутри приложения и по email, личные напоминания и обработку сбоев доставки.
8. **Проверить и протестировать MVP:** полный учебный цикл, права доступа, граничные сроки, повторные запросы и сохранность работ.

OpenAPI и необходимые тесты развивать вместе с каждым этапом реализации.
