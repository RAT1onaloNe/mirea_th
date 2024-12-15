# Lаb1
rat1onal@yandex.ru

# Анализ встроенного пакета dplyr

## Цель работы

1.  Развить практические навыки использования языка программирования R
    для обработки данных
2.  Закрепить знания базовых типов данных языка R
3.  Развить практические навыки использования функций обработки данных
    пакета dplyr – функции select(), filter(), mutate(), arrange(),
    group_by()

## Исходные данные

1.  Windows 11
2.  Git
3.  Rstudio
4.  dplyr

## План

1.  Ознакомиться с данными
2.  Ответить на поставленные вопросы, используя dplyr

## Шаги

1.  Для начала прохождения курса необходимо установить пакет
    nyclights13.

``` r
#install.packages("nycflights13")
```

1.  Загружаем библиотеки.

``` r
library(nycflights13)
library(dplyr)
```


    Присоединяю пакет: 'dplyr'

    Следующие объекты скрыты от 'package:stats':

        filter, lag

    Следующие объекты скрыты от 'package:base':

        intersect, setdiff, setequal, union

1.  Переходим непосредственно к анализу набора данных nycflights13 и
    выполнению заданий:

<!-- -->

1.  Сколько встроенных в пакет nycflights13 датафреймов?

``` r
nrow(data(package = "nycflights13")$results)
```

    [1] 5

1.  Сколько строк в каждом датафрейме?

``` r
tibble(
  flights = nrow(flights),
  airlines = nrow(airlines),
  airports = nrow(airports),
  planes = nrow(planes),
  weather = nrow(weather)
)
```

    # A tibble: 1 × 5
      flights airlines airports planes weather
        <int>    <int>    <int>  <int>   <int>
    1  336776       16     1458   3322   26115

1.  Сколько столбцов в каждом датафрейме?

``` r
tibble(
  flights = ncol(flights),
  airlines = ncol(airlines),
  airports = ncol(airports),
  planes = ncol(planes),
  weather = ncol(weather)
)
```

    # A tibble: 1 × 5
      flights airlines airports planes weather
        <int>    <int>    <int>  <int>   <int>
    1      19        2        8      9      15

1.  Как просмотреть примерный вид датафрейма?

``` r
glimpse(flights)
```

    Rows: 336,776
    Columns: 19
    $ year           <int> 2013, 2013, 2013, 2013, 2013, 2013, 2013, 2013, 2013, 2…
    $ month          <int> 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1…
    $ day            <int> 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1…
    $ dep_time       <int> 517, 533, 542, 544, 554, 554, 555, 557, 557, 558, 558, …
    $ sched_dep_time <int> 515, 529, 540, 545, 600, 558, 600, 600, 600, 600, 600, …
    $ dep_delay      <dbl> 2, 4, 2, -1, -6, -4, -5, -3, -3, -2, -2, -2, -2, -2, -1…
    $ arr_time       <int> 830, 850, 923, 1004, 812, 740, 913, 709, 838, 753, 849,…
    $ sched_arr_time <int> 819, 830, 850, 1022, 837, 728, 854, 723, 846, 745, 851,…
    $ arr_delay      <dbl> 11, 20, 33, -18, -25, 12, 19, -14, -8, 8, -2, -3, 7, -1…
    $ carrier        <chr> "UA", "UA", "AA", "B6", "DL", "UA", "B6", "EV", "B6", "…
    $ flight         <int> 1545, 1714, 1141, 725, 461, 1696, 507, 5708, 79, 301, 4…
    $ tailnum        <chr> "N14228", "N24211", "N619AA", "N804JB", "N668DN", "N394…
    $ origin         <chr> "EWR", "LGA", "JFK", "JFK", "LGA", "EWR", "EWR", "LGA",…
    $ dest           <chr> "IAH", "IAH", "MIA", "BQN", "ATL", "ORD", "FLL", "IAD",…
    $ air_time       <dbl> 227, 227, 160, 183, 116, 150, 158, 53, 140, 138, 149, 1…
    $ distance       <dbl> 1400, 1416, 1089, 1576, 762, 719, 1065, 229, 944, 733, …
    $ hour           <dbl> 5, 5, 5, 5, 6, 5, 6, 6, 6, 6, 6, 6, 6, 6, 6, 5, 6, 6, 6…
    $ minute         <dbl> 15, 29, 40, 45, 0, 58, 0, 0, 0, 0, 0, 0, 0, 0, 0, 59, 0…
    $ time_hour      <dttm> 2013-01-01 05:00:00, 2013-01-01 05:00:00, 2013-01-01 0…

1.  Сколько компаний-перевозчиков (carrier) учитывают эти наборы данных
    (представлено в наборах данных)?

``` r
flights %>% 
  summarise(carriers_count = n_distinct(carrier)) %>% 
  pull(carriers_count)
```

    [1] 16

1.  Сколько рейсов принял аэропорт John F Kennedy Intl в мае?

``` r
flights %>% 
  filter(origin == "JFK", month == 5) %>% 
  count() %>% 
  pull(n)
```

    [1] 9397

1.  Какой самый северный аэропорт?

``` r
airports %>% 
  slice_max(lat, n = 1) %>% 
  knitr::kable()
```

<table>
<thead>
<tr class="header">
<th style="text-align: left;">faa</th>
<th style="text-align: left;">name</th>
<th style="text-align: right;">lat</th>
<th style="text-align: right;">lon</th>
<th style="text-align: right;">alt</th>
<th style="text-align: right;">tz</th>
<th style="text-align: left;">dst</th>
<th style="text-align: left;">tzone</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: left;">EEN</td>
<td style="text-align: left;">Dillant Hopkins Airport</td>
<td style="text-align: right;">72.27083</td>
<td style="text-align: right;">42.89833</td>
<td style="text-align: right;">149</td>
<td style="text-align: right;">-5</td>
<td style="text-align: left;">A</td>
<td style="text-align: left;">NA</td>
</tr>
</tbody>
</table>

1.  Какой аэропорт самый высокогорный (находится выше всех над уровнем
    моря)?

``` r
airports %>% 
  slice_max(alt, n = 1) %>% 
  knitr::kable()
```

<table>
<thead>
<tr class="header">
<th style="text-align: left;">faa</th>
<th style="text-align: left;">name</th>
<th style="text-align: right;">lat</th>
<th style="text-align: right;">lon</th>
<th style="text-align: right;">alt</th>
<th style="text-align: right;">tz</th>
<th style="text-align: left;">dst</th>
<th style="text-align: left;">tzone</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: left;">TEX</td>
<td style="text-align: left;">Telluride</td>
<td style="text-align: right;">37.95376</td>
<td style="text-align: right;">-107.9085</td>
<td style="text-align: right;">9078</td>
<td style="text-align: right;">-7</td>
<td style="text-align: left;">A</td>
<td style="text-align: left;">America/Denver</td>
</tr>
</tbody>
</table>

1.  Какие бортовые номера у самых старых самолетов?

``` r
planes %>% 
  slice_min(year, n = 1) %>% 
  select(tailnum) %>% 
  knitr::kable()
```

<table>
<thead>
<tr class="header">
<th style="text-align: left;">tailnum</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: left;">N381AA</td>
</tr>
</tbody>
</table>

1.  Какая средняя температура воздуха была в сентябре в аэропорту John F
    Kennedy Intl (в градусах Цельсия).

``` r
weather %>%
  filter(origin == "JFK", month == 9) %>%
  summarise(avg_temp_c = mean((temp - 32) * 5/9, na.rm = TRUE)) %>%
  knitr::kable()
```

<table>
<thead>
<tr class="header">
<th style="text-align: right;">avg_temp_c</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: right;">19.38764</td>
</tr>
</tbody>
</table>

1.  Самолеты какой авиакомпании совершили больше всего вылетов в июне?

``` r
flights %>%
  filter(month == 6) %>%
  count(carrier, sort = TRUE) %>%
  head(1) %>%
  knitr::kable()
```

<table>
<thead>
<tr class="header">
<th style="text-align: left;">carrier</th>
<th style="text-align: right;">n</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: left;">UA</td>
<td style="text-align: right;">4975</td>
</tr>
</tbody>
</table>

1.  Самолеты какой авиакомпании задерживались чаще других в 2013 году?

``` r
flights %>%
  filter(year == 2013, dep_delay > 0) %>%
  count(carrier, sort = TRUE) %>%
  head(1) %>%
  knitr::kable()
```

<table>
<thead>
<tr class="header">
<th style="text-align: left;">carrier</th>
<th style="text-align: right;">n</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: left;">UA</td>
<td style="text-align: right;">27261</td>
</tr>
</tbody>
</table>

## Оценка результата

Мы загрузили и изучили наборы данных из пакета nycflights13, затем
применили функции из dplyr для решения практических задач.

## Вывод

Мы расширирили навыки работы с dplyr и закрепили умения по анализу
данных: выборке, фильтрации, преобразованию, сортировке, группировке и
суммированию результатов.
