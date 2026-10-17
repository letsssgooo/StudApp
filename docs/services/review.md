# Проверка и доработка

Сервис `review` отвечает за проверку принятого ответа, оценку, комментарий и возврат на доработку.

- Читает ответ из `submissions` и связывает результат проверки с конкретным ответом.
- Владеет оценкой, комментарием и доступностью результата для публикации; `results` получает разрешённую к показу обратную связь.
- Возврат на доработку содержит комментарий и срок; событие проверки передаётся в `notifications`.

## C4: диаграмма компонентов

```mermaid
%%{init: {"theme": "default", "look": "classic", "layout": "dagre", "flowchart": {"htmlLabels": true, "wrappingWidth": 260, "nodeSpacing": 40, "rankSpacing": 60}}}%%
flowchart TB
    web["<b>Web-приложение</b><br/>[Container · Web]<br/>Пользовательские действия"]:::container
    subgraph backend["StudApp backend · Go, модульный монолит · Container"]
        review["<b>review · Проверка</b><br/>[Component · Go]<br/>Оценка, комментарий, публикация<br/>и доработка"]:::focus
        submissions["<b>submissions · Сдача работ</b><br/>[Component · Go]<br/>Принятый ответ, замена и отзыв"]:::component
        administration["<b>administration · Администрирование</b><br/>[Component · Go]<br/>Заведения, группы, членства,<br/>роли и настройки"]:::component
        results["<b>results · Результаты</b><br/>[Component · Go]<br/>Чтение оценок, итог дисциплины и<br/>приватность"]:::component
        notifications["<b>notifications · Уведомления</b><br/>[Component · Go]<br/>Каналы, личные интервалы и<br/>доставка"]:::component
    end
    db[("<b>Учебные записи</b><br/>[Container · PostgreSQL]<br/>Оценки, комментарии и возвраты")]:::container
    web -->|"Проверяет работу и сохраняет<br/>обратную связь"| review
    review -->|"Получает принятый ответ"| submissions
    review -->|"Проверяет полномочия и настройки"| administration
    results -->|"Читает разрешённую к показу<br/>оценку"| review
    review -->|"Передаёт событие проверки"| notifications
    review -->|"Сохраняет проверку и связь с<br/>ответом"| db

    classDef container fill:#d7e8f7,color:#15334f,stroke:#427fae
    classDef component fill:#edf5fc,color:#15334f,stroke:#6b9cc2
    classDef focus fill:#1964a3,color:#fff,stroke:#124b7a
```
