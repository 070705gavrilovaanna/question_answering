# Question Answering with BERT

## Описание проекта
Разработка модели для ответа на вопросы по тексту на английском языке. Модель находит ответ в заданном контексте.

## Ссылка на ноутбук
- [Google Colab](https://colab.research.google.com/drive/1ey3n0AagDjZMbGYUebqic0NElmb_iHTT#scrollTo=oJibym11vQDG)

## Датасет
- **SQuAD 1.1** (Stanford Question Answering Dataset) [Ссылка на датасет](https://www.kaggle.com/datasets/stanfordu/stanford-question-answering-dataset?select=train-v1.1.json)
- Пары вопрос-ответ по статьям Wikipedia
- Задача: найти начало и конец ответа в тексте

## Технологии
- Python
- PyTorch
- Transformers
- BERT
- Matplotlib

## Архитектура модели
- **BertForQuestionAnswering** — предобученная модель на базе `bert-base-uncased`
- 12 слоёв Transformer, скрытая размерность 768, 110M параметров
- Выходной слой: предсказание start_logits и end_logits для каждого токена

## Предобработка данных
- Токенизация BERT
- Фильтрация примеров, где ответ не влезает в контекст или разбивается при токенизации
- Вычисление start/end позиций через offset_mapping
- Padding до 384 токенов, обрезка только контекста (truncation='only_second')

## Обучение
- **Оптимизатор:** Adam (lr=1e-5)
- **Batch size:** 16
- **Эпох:** 3
- **Функция потерь:** CrossEntropyLoss для start и end позиций
- **Метрика:** точность совпадения start/end индексов

## Результаты

### Динамика обучения

| Эпоха | Train Loss | Train Acc | Val Loss | Val Acc |
|-------|------------|-----------|----------|---------|
| 1     | 3.38       | 15.1%     | 2.19     | 32.5%   |
| 2     | 1.82       | 40.7%     | 1.91     | 41.8%   |
| 3     | 1.33       | 51.6%     | 1.89     | 47.9%   |

### Лучшая модель (эпоха 3)
- **Val Loss:** 1.89
- **Val Accuracy:** 47.9%

### Тестирование на  примерах

Вопрос: what is the capital of France?
Ответ: paris (верно)

Вопрос: where is the Eiffel Tower?
Ответ: paris (верно)

Вопрос: what is the name of Harry Potter's house?
Ответ: gryffindor house (верно)

Вопрос: what type of group is XG?
Ответ: musical group based in soth korea (верно)


## Ключевые особенности реализации
1. Правильное вычисление позиций ответа через offset_mapping
2. Учет специальных токенов ([SEP] для разделения вопрос/контекст)
3. Сохранение лучшей модели по val_loss

## Выводы
- Модель научилась находить ответы на фактологические вопросы
- Accuracy на валидации: 47.9%
- Хорошо работает на простых вопросах (столицы, местоположение)
- Качество ответа зависит от формулировки вопроса и структуры контекста
