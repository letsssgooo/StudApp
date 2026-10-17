# Уведомления и напоминания

Сервис `notifications` отвечает за учебные уведомления внутри приложения и по email, настройки каналов и личные напоминания.

- Получает события от `assignments`, `schedule` и `review` после успешного учебного изменения.
- Владеет настройками доставки и личными интервалами; срок задания предоставляет `assignments`.
- Сбой email-доставки не отменяет принятую работу.

## C4: диаграмма компонентов

```mermaid
%%{init: {"theme": "default", "look": "classic", "layout": "dagre", "flowchart": {"htmlLabels": true, "wrappingWidth": 260, "nodeSpacing": 40, "rankSpacing": 60}}}%%
flowchart TB
    web["<b>Web-приложение</b><br/>[Container · Web]<br/>Пользовательские действия"]:::container
    subgraph backend["StudApp backend · Go, модульный монолит · Container"]
        notifications["<b>notifications · Уведомления</b><br/>[Component · Go]<br/>Каналы, личные интервалы и<br/>доставка"]:::focus
        assignments["<b>assignments · Задания и дедлайны</b><br/>[Component · Go]<br/>Условия, сроки, аудитория и<br/>команды задания"]:::component
        schedule["<b>schedule · Расписание</b><br/>[Component · Go]<br/>Недели, занятия, перенос и<br/>отмена"]:::component
        review["<b>review · Проверка</b><br/>[Component · Go]<br/>Оценка, комментарий, публикация<br/>и доработка"]:::component
        administration["<b>administration · Администрирование</b><br/>[Component · Go]<br/>Заведения, группы, членства,<br/>роли и настройки"]:::component
    end
    email["<b>Email-доставка</b><br/>[External Software System]<br/>Доставка уведомлений по email"]:::external
    web -->|"Настраивает каналы и получает<br/>события"| notifications
    assignments -->|"Передаёт события задания и срока"| notifications
    schedule -->|"Передаёт перенос или отмену"| notifications
    review -->|"Передаёт событие проверки"| notifications
    notifications -->|"Уточняет аудиторию получателей"| administration
    notifications -->|"Передаёт сообщение включённого<br/>канала"| email

    classDef container fill:#d7e8f7,color:#15334f,stroke:#427fae
    classDef component fill:#edf5fc,color:#15334f,stroke:#6b9cc2
    classDef focus fill:#1964a3,color:#fff,stroke:#124b7a
    classDef external fill:#eceff2,color:#333,stroke:#777
```
