# Схемы проекта: система онлайн-записи для шиномонтажа

## 1. Архитектура

```mermaid
flowchart TD
    C["Клиент<br/>Сайт онлайн-записи"]
    A["Администратор<br/>Расписание, ожидание"]
    O["Владелец<br/>Отчёты, выручка"]
    S["Веб-сервер<br/>Логика записи, расчёт цены, отчёты"]
    D[("База данных<br/>Клиенты, записи, услуги, боксы")]
    C --> S
    A --> S
    O --> S
    S --> D
```

## 2. Процесс «как есть»

```mermaid
flowchart LR
    A["Клиент обращается<br/>Звонок, чат, визит"] --> B["Запись в тетрадь<br/>Двойные записи"]
    B --> C["Клиент приезжает<br/>Очередь в сезон"]
    C --> D["Работа в боксе<br/>Время не учитывается"]
    D --> E["Запись на бумаге<br/>Расчёт вручную"]
    E --> F["Итоги за месяц<br/>Выручку не свести"]
    style B fill:#FAECE7,stroke:#993C1D,color:#712B13
    style C fill:#FAECE7,stroke:#993C1D,color:#712B13
    style E fill:#FAECE7,stroke:#993C1D,color:#712B13
    style F fill:#FAECE7,stroke:#993C1D,color:#712B13
```

Красным отмечены проблемные места процесса.

## 3. Процесс «как будет»

```mermaid
flowchart LR
    subgraph Client["Клиент"]
        C1["Заявка с сайта"]
        C2["Новое время или ожидание"]
    end
    subgraph Admin["Администратор"]
        A1["Заявка по звонку или визиту"]
        A2["Ведёт список ожидания"]
        A3["Отмечает работу выполненной"]
    end
    subgraph System["Система"]
        S1{"Слот свободен?"}
        S2["Запись создана, бокс занят"]
        S3["Выгрузка в Excel"]
    end
    C1 --> S1
    A1 --> S1
    S1 -- Да --> S2
    S1 -- Нет --> C2
    C2 --> A2
    A2 -. При отмене .-> S2
    S2 --> A3
    A3 --> S3
```

## 4. ER-диаграмма базы данных

```mermaid
erDiagram
    CLIENT ||--o{ CAR : owns
    CLIENT ||--o{ BOOKING : makes
    CAR ||--o{ BOOKING : serviced
    SERVICE ||--o{ BOOKING : included
    BOX ||--o{ BOOKING : hosts
    CLIENT ||--o{ WAITLIST : waits
    SERVICE ||--o{ WAITLIST : wants
    CLIENT {
        int id PK
        string name
        string phone
    }
    CAR {
        int id PK
        int client_id FK
        string plate
        int wheel_size
    }
    SERVICE {
        int id PK
        string title
        int duration_min
        int price
    }
    BOX {
        int id PK
        string title
    }
    BOOKING {
        int id PK
        int client_id FK
        int car_id FK
        int service_id FK
        int box_id FK
        datetime start_time
        datetime end_time
        string status
    }
    WAITLIST {
        int id PK
        int client_id FK
        int service_id FK
        date wanted_date
    }
```
