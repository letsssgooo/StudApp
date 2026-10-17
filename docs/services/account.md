# Кабинет, вход и учебный контекст

Сервис `account` отвечает за учётную запись, вход, выход и выбор учебного заведения.

- Владеет данными учётной записи пользователя.
- Получает доступные заведения, членства и роли из `administration`.
- Смена заведения использует права выбранного контекста; роли между заведениями не переносятся.

## C4: диаграмма компонентов

```mermaid
%%{init: {"theme": "default", "look": "classic", "layout": "dagre", "flowchart": {"htmlLabels": true, "wrappingWidth": 260, "nodeSpacing": 40, "rankSpacing": 60}}}%%
flowchart TB
    web["<b>Web-приложение</b><br/>[Container · Web]<br/>Пользовательские действия"]:::container
    subgraph backend["StudApp backend · Go, модульный монолит · Container"]
        account["<b>account · Кабинет</b><br/>[Component · Go]<br/>Учётная запись и выбор<br/>контекста"]:::focus
        administration["<b>administration · Администрирование</b><br/>[Component · Go]<br/>Заведения, группы, членства,<br/>роли и настройки"]:::component
    end
    db[("<b>Учебные записи</b><br/>[Container · PostgreSQL]<br/>Учётные записи")]:::container
    web -->|"Входит, выбирает контекст,<br/>выходит"| account
    account -->|"Получает членства и доступные<br/>заведения"| administration
    account -->|"Читает и сохраняет учётную<br/>запись"| db

    classDef container fill:#d7e8f7,color:#15334f,stroke:#427fae
    classDef component fill:#edf5fc,color:#15334f,stroke:#6b9cc2
    classDef focus fill:#1964a3,color:#fff,stroke:#124b7a
```
