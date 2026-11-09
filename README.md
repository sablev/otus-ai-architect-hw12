# Потоки данных и Feature Store для TechnoMart

Архитектура data pipelines для рекомендаций TechnoMart: сбор и очистка
данных, Feature Store, эмбеддинги. Учебный проект курса
OTUS «AI Architect» (ДЗ 12).

## Онлайн-версия

Описание решения и интерактивные диаграммы (LikeC4):

- [Описание потоков данных и выбора хранилищ](RESULT.md)
- [Приём и очистка данных](https://sablev.github.io/otus-ai-architect-hw12/#/view/index)
- [Признаки, обучение и онлайн-выдача](https://sablev.github.io/otus-ai-architect-hw12/#/view/features-serving)
- [Эмбеддинги и предгенерация описаний](https://sablev.github.io/otus-ai-architect-hw12/#/view/embeddings-generation)

## Скоуп

Продолжение MVP из ДЗ 05: сайт, слот `product_card_cross_sell`, item-to-item
и ранжирование по правилам. Данные каталога, кликов и покупок проходят
через Data Lake к признакам и рекомендациям.

Это проект архитектуры; сервисы не реализованы, производительность
не измерялась. Исходные решения — в [контексте](references/previous-decisions.md),
допущения и открытые вопросы — в [основном описании](RESULT.md).

## Состав репозитория

| Путь | Содержимое |
|---|---|
| `RESULT.md` | Основное описание: источники, преобразования, выбор хранилищ и Data Governance |
| `DESIGN_NOTES.md` | Доставка событий, историческая корректность признаков, публикация и откат |
| `likec4/` | Модель LikeC4 и Docker Compose для локального просмотра |
| `references/previous-decisions.md` | Исходные решения ДЗ 05 и краткое изложение ADR-009 из ДЗ 09 |
| `.github/workflows/pages.yml` | Сборка диаграмм и публикация на GitHub Pages |

## Ключевые архитектурные решения

- **Доставка:** PostgreSQL outbox → RabbitMQ → S3, повторы устраняются по `event_id`.
- **ELT:** Airflow и Python/DuckDB преобразуют raw-данные в проверенный Parquet.
- **Признаки:** Feast связывает исторические значения в S3/Parquet с online Redis.
- **Релиз:** манифест закрепляет совместимые версии данных и моделей для выдачи и отката.
- **Генерация:** LLM и Qdrant работают офлайн; Fact Checker проверяет описания, fallback — шаблон.
