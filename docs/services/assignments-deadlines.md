# Задания и дедлайны

Сервис `assignments` владеет заданиями, их аудиторией, публикацией, дедлайнами и составом команд. Команду задания назначает преподаватель.

- Черновик скрыт от студентов; отложенная публикация выполняется после заданного времени.
- Для успешно принятой файловой работы своевременность определяется началом загрузки.
- Отмена задания сохраняет принятые работы; общее продление снимает опоздание с работ, ставших своевременными, через `submissions`.

## C4: диаграмма компонентов

```mermaid
%%{init: {"theme": "default", "look": "classic", "layout": "dagre", "flowchart": {"htmlLabels": true, "wrappingWidth": 260, "nodeSpacing": 40, "rankSpacing": 60}}}%%
flowchart TB
    web["<b>Web-приложение</b><br/>[Container · Web]<br/>Пользовательские действия"]:::container
    subgraph backend["StudApp backend · Go, модульный монолит · Container"]
        assignments["<b>assignments · Задания и дедлайны</b><br/>[Component · Go]<br/>Условия, сроки, аудитория и<br/>команды задания"]:::focus
        administration["<b>administration · Администрирование</b><br/>[Component · Go]<br/>Заведения, группы, членства,<br/>роли и настройки"]:::component
        submissions["<b>submissions · Сдача работ</b><br/>[Component · Go]<br/>Принятый ответ, замена и отзыв"]:::component
        notifications["<b>notifications · Уведомления</b><br/>[Component · Go]<br/>Каналы, личные интервалы и<br/>доставка"]:::component
    end
    db[("<b>Учебные записи</b><br/>[Container · PostgreSQL]<br/>Задания, правила сроков и<br/>команды")]:::container
    web -->|"Готовит задание, меняет срок или<br/>команду"| assignments
    assignments -->|"Проверяет полномочия и аудиторию"| administration
    assignments -->|"Согласует отметки после<br/>продления"| submissions
    assignments -->|"Передаёт публикацию и изменения"| notifications
    assignments -->|"Сохраняет задание, сроки и<br/>команду"| db

    classDef container fill:#d7e8f7,color:#15334f,stroke:#427fae
    classDef component fill:#edf5fc,color:#15334f,stroke:#6b9cc2
    classDef focus fill:#1964a3,color:#fff,stroke:#124b7a
```
