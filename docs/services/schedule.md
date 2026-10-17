# Расписание и занятие

Сервис `schedule` отвечает за расписание недели, создание, перенос и отмену занятий.

- Владеет сведениями занятий; связанные материалы и задания получает из `materials` и `assignments`.
- Перенос и отмена занятия не изменяют автоматически сроки заданий и принятые работы.
- Передаёт сведения о переносах и отменах в `notifications`.

## C4: диаграмма компонентов

```mermaid
%%{init: {"theme": "default", "look": "classic", "layout": "dagre", "flowchart": {"htmlLabels": true, "wrappingWidth": 260, "nodeSpacing": 40, "rankSpacing": 60}}}%%
flowchart TB
    web["<b>Web-приложение</b><br/>[Container · Web]<br/>Пользовательские действия"]:::container
    subgraph backend["StudApp backend · Go, модульный монолит · Container"]
        schedule["<b>schedule · Расписание</b><br/>[Component · Go]<br/>Недели, занятия, перенос и<br/>отмена"]:::focus
        administration["<b>administration · Администрирование</b><br/>[Component · Go]<br/>Заведения, группы, членства,<br/>роли и настройки"]:::component
        materials["<b>materials · Материалы</b><br/>[Component · Go]<br/>Учебные файлы, ссылки и<br/>публикация"]:::component
        assignments["<b>assignments · Задания и дедлайны</b><br/>[Component · Go]<br/>Условия, сроки, аудитория и<br/>команды задания"]:::component
        notifications["<b>notifications · Уведомления</b><br/>[Component · Go]<br/>Каналы, личные интервалы и<br/>доставка"]:::component
    end
    db[("<b>Учебные записи</b><br/>[Container · PostgreSQL]<br/>Занятия и связи")]:::container
    web -->|"Читает неделю и управляет<br/>занятием"| schedule
    schedule -->|"Проверяет полномочия и учебные<br/>связи"| administration
    schedule -->|"Получает доступные материалы<br/>занятия"| materials
    schedule -->|"Получает доступные задания<br/>занятия"| assignments
    schedule -->|"Передаёт перенос или отмену"| notifications
    schedule -->|"Читает и сохраняет занятие"| db

    classDef container fill:#d7e8f7,color:#15334f,stroke:#427fae
    classDef component fill:#edf5fc,color:#15334f,stroke:#6b9cc2
    classDef focus fill:#1964a3,color:#fff,stroke:#124b7a
```
