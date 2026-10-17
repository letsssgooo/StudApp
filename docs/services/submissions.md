# Сдача работ

Сервис `submissions` отвечает за индивидуальную и командную сдачу, замену и отзыв ответа. Владеет текущей работой, связями с вложениями и фактом принятия.

- Явная сдача подтверждается после успешного сохранения; технический повтор не создаёт дубль.
- Неуспешная замена сохраняет прежний ответ.
- Правила приёма, сроки и состав команды получает из `assignments`; принятый ответ предоставляет `review`.

## C4: диаграмма компонентов

```mermaid
%%{init: {"theme": "default", "look": "classic", "layout": "dagre", "flowchart": {"htmlLabels": true, "wrappingWidth": 260, "nodeSpacing": 40, "rankSpacing": 60}}}%%
flowchart TB
    web["<b>Web-приложение</b><br/>[Container · Web]<br/>Пользовательские действия"]:::container
    subgraph backend["StudApp backend · Go, модульный монолит · Container"]
        submissions["<b>submissions · Сдача работ</b><br/>[Component · Go]<br/>Принятый ответ, замена и отзыв"]:::focus
        administration["<b>administration · Администрирование</b><br/>[Component · Go]<br/>Заведения, группы, членства,<br/>роли и настройки"]:::component
        assignments["<b>assignments · Задания и дедлайны</b><br/>[Component · Go]<br/>Условия, сроки, аудитория и<br/>команды задания"]:::component
        review["<b>review · Проверка</b><br/>[Component · Go]<br/>Оценка, комментарий, публикация<br/>и доработка"]:::component
    end
    db[("<b>Учебные записи</b><br/>[Container · PostgreSQL]<br/>Ответы, принадлежность и факты<br/>сдачи")]:::container
    web -->|"Сдаёт, заменяет или отзывает<br/>ответ"| submissions
    submissions -->|"Проверяет актуальное членство"| administration
    submissions -->|"Получает правила, срок и состав<br/>команды"| assignments
    review -->|"Читает принятый ответ"| submissions
    submissions -->|"Сохраняет ответ и факт сдачи"| db

    classDef container fill:#d7e8f7,color:#15334f,stroke:#427fae
    classDef component fill:#edf5fc,color:#15334f,stroke:#6b9cc2
    classDef focus fill:#1964a3,color:#fff,stroke:#124b7a
```
