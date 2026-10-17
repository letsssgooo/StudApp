# Результаты и приватность

Сервис `results` предоставляет результаты работ и итог дисциплины, владеет итогом и настройками приватности.

- Оценки и комментарии читает из `review` без повторного расчёта.
- Итог дисциплины показывает отдельно; отсутствие итога не заменяется неутверждённой формулой.
- Приватность ограничивает чтение поверх базовых прав; собственные результаты доступны при сохранении учебного доступа.

## C4: диаграмма компонентов

```mermaid
%%{init: {"theme": "default", "look": "classic", "layout": "dagre", "flowchart": {"htmlLabels": true, "wrappingWidth": 260, "nodeSpacing": 40, "rankSpacing": 60}}}%%
flowchart TB
    web["<b>Web-приложение</b><br/>[Container · Web]<br/>Пользовательские действия"]:::container
    subgraph backend["StudApp backend · Go, модульный монолит · Container"]
        results["<b>results · Результаты</b><br/>[Component · Go]<br/>Чтение оценок, итог дисциплины и<br/>приватность"]:::focus
        review["<b>review · Проверка</b><br/>[Component · Go]<br/>Оценка, комментарий, публикация<br/>и доработка"]:::component
        administration["<b>administration · Администрирование</b><br/>[Component · Go]<br/>Заведения, группы, членства,<br/>роли и настройки"]:::component
    end
    db[("<b>Учебные записи</b><br/>[Container · PostgreSQL]<br/>Итоги дисциплин и настройки<br/>видимости")]:::container
    web -->|"Читает результаты и меняет<br/>видимость"| results
    results -->|"Получает опубликованные оценки"| review
    results -->|"Проверяет доступ и получает<br/>настройки"| administration
    results -->|"Читает итог и сохраняет<br/>приватность"| db

    classDef container fill:#d7e8f7,color:#15334f,stroke:#427fae
    classDef component fill:#edf5fc,color:#15334f,stroke:#6b9cc2
    classDef focus fill:#1964a3,color:#fff,stroke:#124b7a
```
