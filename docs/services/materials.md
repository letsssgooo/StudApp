# Учебные материалы

Сервис `materials` отвечает за подготовку, публикацию и открытие учебных файлов и ссылок преподавателя или уполномоченного ассистента.

- Владеет записями материалов, связями с занятием, аудиторией и состоянием публикации.
- Студент получает опубликованные материалы своего учебного контекста.
- При распознанной недоступности материала возвращается пустое поле без ошибки.

## C4: диаграмма компонентов

```mermaid
%%{init: {"theme": "default", "look": "classic", "layout": "dagre", "flowchart": {"htmlLabels": true, "wrappingWidth": 260, "nodeSpacing": 40, "rankSpacing": 60}}}%%
flowchart TB
    web["<b>Web-приложение</b><br/>[Container · Web]<br/>Пользовательские действия"]:::container
    subgraph backend["StudApp backend · Go, модульный монолит · Container"]
        materials["<b>materials · Материалы</b><br/>[Component · Go]<br/>Учебные файлы, ссылки и<br/>публикация"]:::focus
        administration["<b>administration · Администрирование</b><br/>[Component · Go]<br/>Заведения, группы, членства,<br/>роли и настройки"]:::component
        schedule["<b>schedule · Расписание</b><br/>[Component · Go]<br/>Недели, занятия, перенос и<br/>отмена"]:::component
    end
    db[("<b>Учебные записи</b><br/>[Container · PostgreSQL]<br/>Материалы и связи с содержимым")]:::container
    web -->|"Готовит, публикует и открывает<br/>материал"| materials
    materials -->|"Проверяет полномочия и аудиторию"| administration
    materials -->|"Проверяет связь с занятием"| schedule
    materials -->|"Читает и сохраняет запись<br/>материала"| db

    classDef container fill:#d7e8f7,color:#15334f,stroke:#427fae
    classDef component fill:#edf5fc,color:#15334f,stroke:#6b9cc2
    classDef focus fill:#1964a3,color:#fff,stroke:#124b7a
```
