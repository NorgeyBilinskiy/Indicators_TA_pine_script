# Индикатор "Combined Rating" для TradingView (Pine Script)

## Обзор
Этот индикатор объединяет три независимых рейтинга, предоставляя трейдерам комплексное понимание поведения рынка и свечных паттернов. Каждый рейтинг отражает определённый аспект рыночной динамики:

1. **Рейтинг характера японской свечи**  
2. **Рейтинг перебития свечей**  
3. **Рейтинг наклона SMA (скользящей средней)**  

Рейтинги отображаются визуально на графике с помощью меток, размещённых над или под свечами в зависимости от контекста. Дополнительно добавлены горизонтальные линии для улучшения визуального восприятия.

---

## Описание рейтингов и методика расчёта

### 1. **Рейтинг характера японской свечи**
Оценивает значимость каждой свечи, учитывая её размер, соотношение тела к размаху и относительное положение.

- **Параметры**:
  - `atr_length`: Количество свечей для расчёта ATR.
  - `spread_ratio`: Минимальное соотношение тела свечи к размаху для значимых свечей.

- **Методика**:
  - Если размах свечи (high - low) меньше половины значения ATR, свеча считается незначимой (`rating = 0`).
  - Для значимых свечей:
    - Положительный рейтинг (`+1`): большая бычья свеча или доминирующая нижняя тень.
    - Отрицательный рейтинг (`-1`): большая медвежья свеча или доминирующая верхняя тень.

### 1. **Рейтинг характера японской свечи**

#### Оценка по верхним и нижним теням

**Верхняя тень** представляет собой расстояние от максимума свечи до её тела:  
- Для бычьей свечи это `(high - close)`.  
- Для медвежьей свечи это `(high - open)`.

Верхняя тень сигнализирует об уровне сопротивления, где цена поднималась, но не смогла удержаться. Чем больше верхняя тень относительно размаха свечи, тем сильнее признак сопротивления, что может указывать на давление со стороны продавцов.

- **Пример оценки верхней тени в рейтинге**:  
  Если длина верхней тени превышает половину размаха свечи, она добавляет отрицательный вклад в рейтинг. Это интерпретируется как медвежий сигнал, поскольку указывает на то, что продавцы смогли отбросить цену вниз.

---

**Нижняя тень** представляет собой расстояние от минимума свечи до её тела:  
- Для бычьей свечи это `(open - low)`.  
- Для медвежьей свечи это `(close - low)`.

Нижняя тень сигнализирует об уровне поддержки, где цена падала, но затем восстановилась. Чем длиннее нижняя тень относительно размаха свечи, тем сильнее это воспринимается как признак давления покупателей.

- **Пример оценки нижней тени в рейтинге**:  
  Если длина нижней тени превышает половину размаха свечи, это добавляет положительный вклад в рейтинг. Такой паттерн интерпретируется как бычий сигнал, поскольку он указывает на то, что покупатели смогли восстановить цену после снижения.

---

#### Взаимосвязь теней с телом свечи
Верхние и нижние тени оцениваются вместе с телом свечи, чтобы выделить значимость паттерна:  
- Если у свечи тело маленькое, но тени длинные, это может указывать на нерешительность на рынке.  
- Если тело свечи большое (превышает установленное значение `spread_ratio * candle_range`), приоритет в рейтинге отдается силе самой свечи, а не теням.  

Такой подход помогает определить, где рыночное давление сосредоточено — в продолжении тренда (большое тело) или в его потенциальном развороте (длинные тени).

---

### 2. **Рейтинг перебития свечей**

Этот рейтинг используется для оценки поведения текущей свечи относительно диапазона предыдущей свечи, чтобы выявить признаки продолжения или изменения тренда. Оценка проводится на основе сравнения цены закрытия текущей свечи с максимумом и минимумом предыдущей свечи.

---

#### Основные принципы

1. **Бычий рейтинг**:
   - **`+2`** — если цена закрытия текущей свечи находится выше максимума предыдущей свечи.  
     Это сильный сигнал продолжения восходящего тренда.
   - **`+1`** — если цена закрытия текущей свечи остаётся внутри диапазона предыдущей свечи, но предшествующая свеча имела значение `+2`.  
     Такой рейтинг указывает на временную паузу в тренде с возможным возобновлением движения вверх.

2. **Медвежий рейтинг**:
   - **`-2`** — если цена закрытия текущей свечи находится ниже минимума предыдущей свечи.  
     Это сильный сигнал продолжения нисходящего тренда.
   - **`-1`** — если цена закрытия текущей свечи остаётся внутри диапазона предыдущей свечи, но предшествующая свеча имела значение `-2`.  
     Такой рейтинг сигнализирует о замедлении нисходящего тренда, но его возможном возобновлении.

3. **Нейтральные свечи**:
   - Если цена открытия и закрытия текущей свечи совпадают (`open = close`), то оценка зависит от соотношения теней:
     - **`+1`** — если верхняя тень длиннее нижней.
     - **`-1`** — если нижняя тень длиннее верхней.

---

#### Методика расчёта

1. **Анализ пробоя диапазона**:
   - Если текущая цена закрытия превышает максимум предыдущей свечи, это указывает на сильное давление покупателей.
   - Если текущая цена закрытия опускается ниже минимума предыдущей свечи, это указывает на доминирование продавцов.

2. **Сценарии внутри диапазона**:
   - Если текущая свеча остаётся в пределах диапазона предыдущей свечи:
     - Рейтинг снижается с `+2` до `+1` или с `-2` до `-1`, что указывает на ослабление импульса.
   - В этом случае предыдущий тренд ещё может продолжиться, но требуется подтверждение на следующих свечах.

3. **Нейтральные свечи с одинаковыми открытием и закрытием**:
   - Длинные тени относительно короткого тела свечи или его отсутствия указывают на рыночную неопределённость.

---

### 3. **Рейтинг наклона SMA**
Оценивает направление краткосрочного тренда на основе наклона 10-периодной простой скользящей средней (SMA).

- **Методика**:
  - `+1`: SMA растёт (текущее значение больше предыдущего).
  - `-1`: SMA снижается (текущее значение меньше предыдущего).

---

## Визуальное отображение
1. **Рейтинг характера японской свечи**:
   - Метки отображаются над или под свечами, зелёный цвет для `+1`, красный для `-1`.

2. **Рейтинг перебития свечей**:
   - Метки слегка смещены от свечей, чтобы избежать наложения, тёмно-зелёный цвет для `+2`, тёмно-красный для `-2`.

3. **Рейтинг наклона SMA**:
   - Метки размещаются выше или ниже свечей, чёрный цвет для `+1` или `-1`.

4. **Границы**:
   - Горизонтальные линии, параллельные максимумам и минимумам свечей, добавлены для улучшения визуализации.

---

## Настройки
### Входные параметры
- `atr_length`: Период для расчёта ATR (по умолчанию 10).
- `spread_ratio`: Пороговое значение для значимых свечей (по умолчанию 0.5).
- `sma_length`: Период SMA (по умолчанию 10).

### Цвета
- Метки и линии имеют разные цвета для удобства восприятия.

---

## Применение
1. Добавьте скрипт на ваш график в TradingView.
2. Анализируйте комбинированные рейтинги для оценки свечных паттернов и подтверждения трендов.
3. Используйте горизонтальные линии для разделения визуальных областей и упрощения анализа.

---

## Примечания
Индикатор рекомендуется использовать вместе с другими инструментами технического анализа и не полагаться только на его данные. Перед использованием в реальной торговле протестируйте его на исторических данных.

__Не является инвестиционной рекомендацией.__

__Не является торговым сигналом.__

---
