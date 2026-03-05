# Segment-Performance-Analysis-in-Hospitality

### Revenue Management Project

**Dataset:** Hotel Booking Demand Dataset
**Fuente:** Kaggle
**Periodo analizado:** Julio 2015 – Agosto 2017
**Duración del dataset:** 793 días

Este proyecto analiza el rendimiento de los segmentos de mercado en un hotel utilizando métricas clásicas de Revenue Management.

El objetivo es responder una pregunta estratégica clave:

**¿El segmento que más ocupa el hotel es también el que más valor económico genera?**

---

# 1. Comprensión del Dataset

Cada fila del dataset representa:

**Una reserva completa realizada en el hotel.**

Una reserva puede incluir varias noches, por lo que **no equivale a una habitación ocupada por día**.

Esto es importante porque muchas métricas hoteleras se calculan **por noche de habitación (room night)**.

---

# 2. Clasificación de las columnas

## Dimensión principal

Para este análisis se utiliza la dimensión:

**market_segment**

Esta variable clasifica las reservas según el canal o tipo de cliente.

Ejemplos:

* Online TA
* Offline TA/TO
* Direct
* Corporate
* Groups

---

## Métricas base del dataset

Estas columnas permiten calcular el consumo real del inventario.

* stays_in_weekend_nights
* stays_in_week_nights

---

## Métrica existente en el dataset

El dataset incluye:

**adr**

Pero es importante entender qué representa realmente.

Este valor es **ADR por reserva**, no ADR por segmento.

La fórmula implícita es:

ADR_reserva = revenue_reserva / noches_reserva

Por lo tanto **no se puede usar directamente para comparar segmentos**.

Para hacerlo correctamente debemos recalcular ADR.

---

# 3. Variables necesarias para el análisis

Para responder la pregunta estratégica necesitamos calcular:

* Room Nights
* Revenue
* ADR por segmento
* Revenue contribution %
* Segment Performance Index

---

## Room Nights

Representa el número total de noches de habitación consumidas.

Se calcula como:

```
room_nights = stays_in_weekend_nights + stays_in_week_nights
```

Esta métrica representa **la ocupación generada por cada segmento**.

---

## Revenue

El dataset no incluye revenue explícitamente.

Pero puede derivarse usando:

```
revenue = adr × room_nights
```

Esto permite calcular ingresos agregados por segmento.

---

# 4. Estimación del inventario del hotel

Para algunas métricas hoteleras como RevPAR es necesario conocer:

**rooms_available**

El dataset no incluye esta variable, por lo que se estimó mediante un método analítico.

---

## Procedimiento utilizado

1. Se eliminaron reservas canceladas (`is_canceled = 0`) porque no consumen inventario.

2. Se calcularon las noches de cada reserva:

```
room_nights = stays_in_weekend_nights + stays_in_week_nights
```

3. Se construyó la fecha de llegada combinando:

* arrival_date_year
* arrival_date_month
* arrival_date_day_of_month

4. Cada reserva fue expandida a todas las noches que ocupa.

5. Se calculó el número de habitaciones ocupadas cada día.

---

## Resultado

Máxima ocupación observada en el dataset:

**409 habitaciones**

Fecha estimada:

**13 de agosto de 2016**

Esto permite inferir que el hotel tiene aproximadamente **409 habitaciones**.

---

# 5. Periodo del dataset

El dataset cubre:

**2015-07-01 → 2017-08-31**

Número de días únicos:

**793 días**

Esto permite calcular el inventario total del periodo:

```
Rooms available = habitaciones × días
Room-nights available = habitaciones × días
```

---

# 6. Métricas utilizadas en el análisis

## Room Nights

```
Room Nights = SUM(room_nights)
```

---

## Revenue

```
Revenue = SUM(revenue)
```

---

## ADR por segmento

Es fundamental **no usar el promedio simple de ADR**.

Incorrecto:

```
AVG(adr)
```

Correcto:

```
ADR_segment = SUM(revenue) / SUM(room_nights)
```

Esto evita sesgos cuando las reservas tienen distinta duración.

---

## Revenue Contribution %

Mide qué porcentaje del revenue total genera cada segmento.

```
Revenue % = segment_revenue / total_revenue
```

---

## Room Nights %

Mide la proporción de ocupación generada por cada segmento.

```
Room Nights % = segment_room_nights / total_room_nights
```

---

## Segment Performance Index (SPI)

Este indicador mide si un segmento genera más o menos valor relativo que su volumen.

```
SPI = Revenue % / Room Nights %
```

Interpretación:

SPI > 1
El segmento genera más revenue relativo que su ocupación.

SPI = 1
El segmento tiene rendimiento promedio.

SPI < 1
El segmento genera menos revenue relativo que su ocupación.

---

# 7. Resultados del análisis

| Segmento      | Room Nights | Revenue | ADR   | Revenue % | Room Nights % | SPI  |
| ------------- | ----------- | ------- | ----- | --------- | ------------- | ---- |
| Online TA     | 201,452     | 23.94M  | 118.8 | 56%       | 49.6%         | 1.13 |
| Offline TA/TO | 94,022      | 8.15M   | 86.7  | 19%       | 23.2%         | 0.82 |
| Direct        | 40,056      | 5.09M   | 127.1 | 11.9%     | 9.9%          | 1.21 |
| Groups        | 58,591      | 4.66M   | 79.7  | 10.9%     | 14.4%         | 0.76 |
| Corporate     | 10,897      | 774K    | 71.0  | 1.8%      | 2.7%          | 0.68 |
| Aviation      | 855         | 87K     | 102.3 | 0.2%      | 0.2%          | 0.97 |

---

# 8. Insights clave

## El segmento con mayor ocupación

El segmento que más ocupa el hotel es:

**Online TA**

Con aproximadamente:

**50% de todas las room nights**

Esto indica una fuerte dependencia del canal OTA.

---

## El segmento con mayor ADR

El segmento con mayor precio promedio es:

**Direct**

ADR ≈ **127**

Esto indica que las reservas directas pagan precios superiores.

---

## El segmento más eficiente

El mejor rendimiento relativo lo tiene:

**Direct**

SPI ≈ **1.21**

Esto significa que genera **más revenue proporcional que su volumen**.

---

## Segmentos que diluyen rentabilidad

Los segmentos con peor rendimiento son:

Groups
Offline TA/TO
Corporate

Estos segmentos generan más ocupación relativa que revenue.

Por ejemplo:

Groups genera:

14.4% de las noches
pero solo
10.9% del revenue.

SPI = **0.76**

---

# 9. Conclusión estratégica

El análisis demuestra que:

**El segmento que más ocupa el hotel no es el que más valor genera.**

Online TA genera gran volumen de ocupación, pero no es el segmento más rentable.

El canal **Direct**, aunque representa menos del 10% de las room nights, muestra el mejor rendimiento económico.

Esto sugiere que una estrategia de revenue management podría enfocarse en:

* incrementar reservas directas
* reducir dependencia de OTA
* revisar pricing de segmentos de grupos
* optimizar el mix de segmentos

---

# 10. Conclusión de negocio

Este tipo de análisis permite responder preguntas clave de Revenue Management:

* ¿Qué segmentos generan valor real?
* ¿Qué canales están diluyendo ADR?
* ¿Dónde conviene invertir marketing y distribución?




