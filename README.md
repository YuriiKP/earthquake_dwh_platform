# Earthquake Data Warehouse Platform

## Описание проекта

Проект представляет собой полноценную платформу для сбора, хранения и анализа данных о землетрясениях с использованием современного стека технологий data engineering.

Платформа реализует классическую ETL-архитектуру с несколькими слоями данных:
- **Raw Layer** - сырые данные от USGS API, хранящиеся в S3 (MinIO)
- **ODS (Operational Data Store)** - операционный слой данных в PostgreSQL
- **DM (Data Mart)** - аналитический слой с агрегированными метриками

## Архитектура

Система состоит из нескольких взаимосвязанных DAG'ов в Apache Airflow:

1. **row_from_api_to_s3** - загрузка данных о землетрясениях из USGS API в S3
2. **row_from_s3_to_pg** - перенос данных из S3 в PostgreSQL ODS слой
3. **fct_avg_day_earthquake** - вычисление средней магнитуды землетрясений по дням
4. **fct_count_day_earthquake** - подсчет количества землетрясений по дням

## Технологический стек

- **Orchestration**: Apache Airflow 2.10.5
- **Storage**: MinIO (S3-compatible), PostgreSQL 13
- **Data Processing**: DuckDB
- **Visualization**: Metabase
- **Infrastructure**: Docker, Docker Compose

## Быстрый старт

### Требования
- Docker
- Docker Compose

### Установка

1. Клонируйте репозиторий:
```bash
git clone <repository-url>
cd Earthquake_DWH_Platform
```

2. Запустите проект:
```bash
docker-compose up -d
```

3. Настройте Airflow Variables в веб-интерфейсе (http://localhost:8080):
   - `ACCESS_KEY` - ключ доступа к MinIO
   - `SECRET_KEY` - секретный ключ MinIO
   - `pg_password` - пароль для PostgreSQL

4. Доступ к сервисам:
   - Airflow UI: http://localhost:8080 (airflow/airflow)
   - MinIO Console: http://localhost:9001 (minioadmin/minioadmin)
   - Metabase: http://localhost:3000

## Структура проекта

```
pet_project/
├── config/          # Конфигурационные файлы
├── dags/           # Airflow DAG'и
├── data/           # Локальное хранилище данных
├── logs/           # Логи Airflow
├── metabase/       # Dockerfile для Metabase
└── docker-compose.yaml
```

## Особенности

- Автоматическая загрузка ежедневных данных о землетрясениях
- Инкрементальная обработка данных с поддержкой catchup
- Надежность: автоматические повторы, сенсоры для проверки зависимостей
- Масштабируемость через CeleryExecutor
- Полная контейнеризация всех компонентов
