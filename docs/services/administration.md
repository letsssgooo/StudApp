# Учебное администрирование

Сервис `administration` управляет заведениями, дисциплинами, группами, членствами, ролями и учебными настройками.

- Предоставляет остальным сервисам актуальные учебные связи и полномочия.
- Получает сведения о пользователе из `account`.
- Операции над занятиями передаёт `schedule`, назначение команд задания — `assignments`, сохраняя полномочия исполнителя.

## C4: диаграмма компонентов

```mermaid
%%{init: {"theme": "default", "look": "classic", "layout": "dagre", "flowchart": {"htmlLabels": true, "wrappingWidth": 260, "nodeSpacing": 40, "rankSpacing": 60}}}%%
flowchart TB
    web["<b>Web-приложение</b><br/>[Container · Web]<br/>Пользовательские действия"]:::container
    subgraph backend["StudApp backend · Go, модульный монолит · Container"]
        administration["<b>administration · Администрирование</b><br/>[Component · Go]<br/>Заведения, группы, членства,<br/>роли и настройки"]:::focus
        account["<b>account · Кабинет</b><br/>[Component · Go]<br/>Учётная запись и выбор<br/>контекста"]:::component
        schedule["<b>schedule · Расписание</b><br/>[Component · Go]<br/>Недели, занятия, перенос и<br/>отмена"]:::component
        assignments["<b>assignments · Задания и дедлайны</b><br/>[Component · Go]<br/>Условия, сроки, аудитория и<br/>команды задания"]:::component
    end
    db[("<b>Учебные записи</b><br/>[Container · PostgreSQL]<br/>Заведения, дисциплины, группы,<br/>членства и настройки")]:::container
    web -->|"Управляет учебным контекстом"| administration
    administration -->|"Получает сведения об участнике"| account
    administration -->|"Передаёт операцию над занятием"| schedule
    administration -->|"Передаёт назначение команды"| assignments
    administration -->|"Читает и сохраняет учебную<br/>структуру"| db

    classDef container fill:#d7e8f7,color:#15334f,stroke:#427fae
    classDef component fill:#edf5fc,color:#15334f,stroke:#6b9cc2
    classDef focus fill:#1964a3,color:#fff,stroke:#124b7a
```
