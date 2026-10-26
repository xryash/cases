[English](#data-platform-architecture) · [Русский](#архитектура-платформы-данных)

# Data Platform Architecture

![Data Platform Architecture](<Data Platform Architecture.png>)

An anonymized architecture diagram of a production data platform I designed and delivered end-to-end — from initial requirements gathering and technology selection to team assembly and production operations.

**Scale:** 20+ TB of compressed data · **Result:** 5x infrastructure cost reduction vs. managed cloud alternatives.

## Highlights

- **Multi-datacenter HA**: ClickHouse cluster sharded across 3 datacenter zones with replication factor 3 — quorum protection against split-brain, zero data loss on single-node failure.
- **Hybrid processing**: Kafka Connect + Debezium for CDC ingestion, Apache Flink for streaming ETL and NRT delivery (~5 min SLA to Silver), Airflow + dbt for batch Gold-layer builds (~1 hour SLA).
- **Medallion data model**: STG (raw, TTL ~1 week for debugging) → INTERMEDIATE (cleansed, deduplicated, cold partitions offloaded to S3) → BUSINESS (curated datasets per use case).
- **Data contracts**: Confluent Schema Registry enforcing producer/consumer compatibility across 15+ topics.
- **Serving layer**: in-house DWH API + MCP endpoint — the same business layer serves BI reporting and AI agents querying ClickHouse.
- **Cost & vendor independence**: fully open-source stack, no licensing fees, no vendor lock-in. ClickHouse compression ratios of 10–20x at 20+ TB scale.
- **Operations**: stateless services on per-DC Kubernetes clusters, deployed independently via Helm + GitLab CI/CD; database migrations applied from a single versioned repository. Prometheus + Grafana + Telegram for metrics and alerting.

## Components

| Layer | Technology | Purpose |
|---|---|---|
| Sources | Source DB 1..N | Production transactional databases |
| Ingestion | Kafka Connect, CDC connectors | Extract, buffer, deliver change events (Avro) |
| Bus | Apache Kafka, Schema Registry | Event backbone, data contracts |
| Streaming | Apache Flink | NRT datamarts, metrics, ML features |
| Batch | Airflow + dbt | Orchestration, data quality, Gold-layer builds |
| Storage | ClickHouse (sharded, 3 DC), S3 | OLAP core, cold tier, report exports |
| Serving | DWH API (REST + MCP), API proxy | Business access for users, services, and AI agents |
| Ops | Kubernetes, Helm, GitLab CI/CD, Prometheus, Grafana | Deployment, migrations, monitoring, alerting |

## Viewing the `.drawio` file

The diagram is stored as [`diagram.drawio`](./diagram.drawio). Ways to open it:

1. **Web (no install)** — go to [app.diagrams.net](https://app.diagrams.net), choose *Open Existing Diagram*, select the file.
2. **Desktop app** — download [draw.io Desktop](https://github.com/jgraph/drawio-desktop/releases), then `File → Open`.
3. **VS Code** — install the *Draw.io Integration* extension (`pkief.drawio-vscode`), click the file in the explorer.
4. **GitHub preview** — this repo includes a rendered PNG above; GitHub also renders `.drawio` XML as plain text for diffs.

## Disclaimer

All business entities, table names, and topology details are anonymized. The diagram demonstrates architecture patterns and decisions, not company-specific implementation.

## Contacts

- LinkedIn: [i-gnatyuk](https://www.linkedin.com/in/i-gnatyuk/)
- Telegram: [@gson_vs_jackson](https://t.me/gson_vs_jackson)
- Email: demospro1996@gmail.com

---

# Архитектура платформы данных

![Архитектура платформы данных](<Data Platform Architecture.png>)

Обфусцированная архитектурная схема продакшен-платформы данных, которую я спроектировал и ввел в эксплуатацию полным циклом — от сбора требований, подбора команды и выбора технологий до эксплуатации.

**Масштаб:** 20+ ТБ сжатых данных · **Результат:** сокращение расходов на инфраструктуру в 5 раз по сравнению с managed-облаками.

## Ключевые решения

- **Multi-DC HA**: ClickHouse, шардированный по 3 зонам датацентров с фактором репликации 3 — защита от split-brain через кворум, гарантия от потери данных при отказе одной ноды.
- **Гибридный процессинг**: Kafka Connect + Debezium для CDC-извлечения, Apache Flink для streaming ETL и NRT-доставки (SLA ~5 мин до Silver), Airflow + dbt для батчевых Gold-слоев (SLA ~1 час).
- **Medallion**: STG (сырые данные, TTL ~неделя для отладки инцидентов) → INTERMEDIATE (очищенные, дедуплицированные; холодные партиции выгружаются на S3) → BUSINESS (подготовленные данные под конкретные бизнес-кейсы).
- **Data contracts**: Confluent Schema Registry контролирует совместимость producer/consumer на 15+ топиках.
- **Serving**: собственная разработка — DWH API + MCP-эндпоинт; единый бизнес-слой обслуживает и BI-отчетность, и AI-агентов, работающих с ClickHouse.
- **Экономия и независимость**: полностью open-source стек + свои наработки — ноль лицензионных платежей, никакого vendor lock-in. Сжатие ClickHouse 10–20x при грамотной структуре таблиц на масштабе 20+ ТБ.
- **Эксплуатация**: stateless-сервисы в K8s (по кластеру на ДЦ), независимый деплой через Helm + GitLab CI/CD; миграции основной БД из единого версионируемого репозитория. Prometheus + Grafana + Telegram для метрик и алертинга.

## Компоненты

| Слой | Технология | Назначение |
|---|---|---|
| Источники | Source DB 1..N | Продакшен-базы данных |
| Извлечение | Kafka Connect, CDC-коннекторы | Extract, буферизация, доставка изменений (Avro) |
| Шина | Apache Kafka, Schema Registry | Event backbone, data contracts |
| Стриминг | Apache Flink | NRT datamarts, метрики, ML-фичи |
| Батч | Airflow + dbt | Оркестрация, data quality, Gold-слой |
| Хранение | ClickHouse (шарды, 3 ДЦ), S3 | OLAP-ядро, холодный слой, выгрузка отчетов |
| Доступ | DWH API (REST + MCP), API proxy | Бизнес-слой для пользователей, сервисов и AI-агентов |
| Эксплуатация | Kubernetes, Helm, GitLab CI/CD, Prometheus, Grafana | Деплой, миграции, мониторинг, алертинг |

## Как открыть файл `.drawio`

Схема лежит в файле [`diagram.drawio`](./diagram.drawio). Варианты просмотра:

1. **Через веб (без установки)** — откройте [app.diagrams.net](https://app.diagrams.net), выберите *Open Existing Diagram* и укажите файл.
2. **Десктопное приложение** — скачайте [draw.io Desktop](https://github.com/jgraph/drawio-desktop/releases), затем `File → Open`.
3. **VS Code** — установите расширение *Draw.io Integration* (`pkief.drawio-vscode`) и откройте файл кликом.
4. **Превью на GitHub** — отрендеренный PNG приведен выше; сам `.drawio` GitHub показывает как XML в diffs.

## Дисклеймер

Все бизнес-сущности, названия таблиц и детали топологии обфусцированы. Схема демонстрирует архитектурные паттерны и решения, а не специфику конкретной компании.

## Контакты

- LinkedIn: [i-gnatyuk](https://www.linkedin.com/in/i-gnatyuk/)
- Telegram: [@gson_vs_jackson](https://t.me/gson_vs_jackson)
- Email: demospro1996@gmail.com
