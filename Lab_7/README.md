# Lаb1
rat1onal@yandex.ru

# Анализ данных сетевого трафика при помощи библиотеки Arrow

## Цель работы

1.  Изучить возможности технологии Apache Arrow для обработки и анализ
    больших данных
2.  Получить навыки применения Arrow совместно с языком программирования
    R
3.  Получить навыки анализа метаинфомации о сетевом трафике
4.  Получить навыки применения облачных технологий хранения, подготовки
    и анализа данных: Yandex Object Storage, Rstudio Server.

## Исходные данные

1.  Windows 11
2.  Rstudio Desktop
3.  Apache Arrow
4.  Yandex Object Storage

## План

1.  Импорт данных через функцию `open_dataset`.
2.  Выполнение заданий, описанных ниже.

## Шаги

1.  Импортируем данные с помощью библиотеки arrow

``` r
library(arrow)
```

    Some features are not enabled in this build of Arrow. Run `arrow_info()` for more information.


    Attaching package: 'arrow'

    The following object is masked from 'package:utils':

        timestamp

``` r
library(dplyr)
```


    Attaching package: 'dplyr'

    The following objects are masked from 'package:stats':

        filter, lag

    The following objects are masked from 'package:base':

        intersect, setdiff, setequal, union

``` r
library(tidyverse)
```

    ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ✔ forcats   1.0.0     ✔ readr     2.1.5
    ✔ ggplot2   3.5.1     ✔ stringr   1.5.1
    ✔ lubridate 1.9.3     ✔ tibble    3.2.1
    ✔ purrr     1.0.2     ✔ tidyr     1.3.1

    ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ✖ lubridate::duration() masks arrow::duration()
    ✖ dplyr::filter()       masks stats::filter()
    ✖ dplyr::lag()          masks stats::lag()
    ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

``` r
#download.file("https://storage.yandexcloud.net/arrow-datasets/tm_data.pqt",destfile = "tm_data.pqt")
df <- arrow::open_dataset(sources = "tm_data.pqt", format = "parquet")
glimpse(df)
```

    FileSystemDataset with 1 Parquet file
    105,747,730 rows x 5 columns
    $ timestamp <double> 1.578326e+12, 1.578326e+12, 1.578326e+12, 1.578326e+12, 1.5…
    $ src       <string> "13.43.52.51", "16.79.101.100", "18.43.118.103", "15.71.108…
    $ dst       <string> "18.70.112.62", "12.48.65.39", "14.51.30.86", "14.50.119.33…
    $ port       <int32> 40, 92, 27, 57, 115, 92, 65, 123, 79, 72, 123, 123, 22, 118…
    $ bytes      <int32> 57354, 11895, 898, 7496, 20979, 8620, 46033, 1500, 979, 103…
    Call `print()` for full schema details

``` r
glimpse(df)
```

    FileSystemDataset with 1 Parquet file
    105,747,730 rows x 5 columns
    $ timestamp <double> 1.578326e+12, 1.578326e+12, 1.578326e+12, 1.578326e+12, 1.5…
    $ src       <string> "13.43.52.51", "16.79.101.100", "18.43.118.103", "15.71.108…
    $ dst       <string> "18.70.112.62", "12.48.65.39", "14.51.30.86", "14.50.119.33…
    $ port       <int32> 40, 92, 27, 57, 115, 92, 65, 123, 79, 72, 123, 123, 22, 118…
    $ bytes      <int32> 57354, 11895, 898, 7496, 20979, 8620, 46033, 1500, 979, 103…
    Call `print()` for full schema details

1.  Приступаем к выполнению заданий

<!-- -->

1.  Найдите утечку данных из вашей сети

``` r
res_task_a <- df %>% 
  filter((str_detect(src, "^12\\.") | str_detect(src, "^13\\.") | str_detect(src, "^14\\.")) &
         !str_detect(dst, "^12\\.") & !str_detect(dst, "^13\\.") & !str_detect(dst, "^14\\.")) %>%
  group_by(src) %>%
  summarise(sum = sum(bytes), .groups = "drop") %>%
  arrange(desc(sum)) %>%
  slice_head(n = 1) %>%
  select(src)

res_task_a %>% collect() %>% knitr::kable()
```

<table>
<thead>
<tr class="header">
<th style="text-align: left;">src</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: left;">13.37.84.125</td>
</tr>
</tbody>
</table>

1.  Найдите утечку данных 2

Определяем рабочее время по пиковому трафику.

``` r
res_task_b1 <- df %>%
  select(timestamp, src, dst, bytes) %>%
  mutate(
    trafic = str_detect(src, "^(12|13|14)\\.") & !str_detect(dst, "^(12|13|14)\\."),
    time = hour(as_datetime(timestamp / 1000))
  ) %>%
  filter(trafic, between(time, 0, 24)) %>%
  group_by(time) %>%
  summarise(trafictime = n(), .groups = "drop") %>%
  arrange(desc(trafictime))

res_task_b1 %>% collect() %>% knitr::kable()
```

<table>
<thead>
<tr class="header">
<th style="text-align: right;">time</th>
<th style="text-align: right;">trafictime</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: right;">16</td>
<td style="text-align: right;">4490576</td>
</tr>
<tr class="even">
<td style="text-align: right;">22</td>
<td style="text-align: right;">4489703</td>
</tr>
<tr class="odd">
<td style="text-align: right;">18</td>
<td style="text-align: right;">4489386</td>
</tr>
<tr class="even">
<td style="text-align: right;">23</td>
<td style="text-align: right;">4488093</td>
</tr>
<tr class="odd">
<td style="text-align: right;">19</td>
<td style="text-align: right;">4487345</td>
</tr>
<tr class="even">
<td style="text-align: right;">21</td>
<td style="text-align: right;">4487109</td>
</tr>
<tr class="odd">
<td style="text-align: right;">17</td>
<td style="text-align: right;">4483578</td>
</tr>
<tr class="even">
<td style="text-align: right;">20</td>
<td style="text-align: right;">4482712</td>
</tr>
<tr class="odd">
<td style="text-align: right;">13</td>
<td style="text-align: right;">169617</td>
</tr>
<tr class="even">
<td style="text-align: right;">7</td>
<td style="text-align: right;">169241</td>
</tr>
<tr class="odd">
<td style="text-align: right;">0</td>
<td style="text-align: right;">169068</td>
</tr>
<tr class="even">
<td style="text-align: right;">3</td>
<td style="text-align: right;">169050</td>
</tr>
<tr class="odd">
<td style="text-align: right;">14</td>
<td style="text-align: right;">169028</td>
</tr>
<tr class="even">
<td style="text-align: right;">6</td>
<td style="text-align: right;">169015</td>
</tr>
<tr class="odd">
<td style="text-align: right;">12</td>
<td style="text-align: right;">168892</td>
</tr>
<tr class="even">
<td style="text-align: right;">10</td>
<td style="text-align: right;">168750</td>
</tr>
<tr class="odd">
<td style="text-align: right;">2</td>
<td style="text-align: right;">168711</td>
</tr>
<tr class="even">
<td style="text-align: right;">11</td>
<td style="text-align: right;">168684</td>
</tr>
<tr class="odd">
<td style="text-align: right;">1</td>
<td style="text-align: right;">168539</td>
</tr>
<tr class="even">
<td style="text-align: right;">4</td>
<td style="text-align: right;">168422</td>
</tr>
<tr class="odd">
<td style="text-align: right;">15</td>
<td style="text-align: right;">168355</td>
</tr>
<tr class="even">
<td style="text-align: right;">5</td>
<td style="text-align: right;">168283</td>
</tr>
<tr class="odd">
<td style="text-align: right;">9</td>
<td style="text-align: right;">168283</td>
</tr>
<tr class="even">
<td style="text-align: right;">8</td>
<td style="text-align: right;">168205</td>
</tr>
</tbody>
</table>

Уточняем утечку данных в рабочие часы (с 16 до 23).

``` r
res_task_b2 <- df %>%
  mutate(time = hour(as_datetime(timestamp / 1000))) %>%
  filter(
    !str_detect(src, "^13\\.37\\.84\\.125"),
    str_detect(src, "^(12|13|14)\\."),
    !str_detect(dst, "^(12|13|14)\\."),
    between(time, 1, 15)
  ) %>%
  group_by(src) %>%
  summarise(sum = sum(bytes), .groups = "drop") %>%
  arrange(desc(sum)) %>%
  slice_head(n = 1) %>%
  select(src)

res_task_b2 %>% collect() %>% knitr::kable()
```

<table>
<thead>
<tr class="header">
<th style="text-align: left;">src</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: left;">12.55.77.96</td>
</tr>
</tbody>
</table>

1.  Найдите утечку данных 3

Выявляем порт с аномальным трафиком.

``` r
res_task_c_data <- df %>%
  filter(
    !str_detect(src, "^13\\.37\\.84\\.125"),
    !str_detect(src, "^12\\.55\\.77\\.96"),
    str_detect(src, "^(12|13|14)\\."),
    !str_detect(dst, "^(12|13|14)\\.")
  ) %>%
  select(src, bytes, port)
```

    Определяем источник трафика для данного порта.

``` r
res_task_c1 <- res_task_c_data %>%
  group_by(port) %>%
  summarise(
    mean = mean(bytes),
    max = max(bytes),
    sum = sum(bytes),
    .groups = "drop"
  ) %>%
  mutate(Raz = max - mean) %>%
  filter(Raz != 0) %>%
  arrange(desc(Raz)) %>%
  slice_head(n = 1) %>%
  collect()

res_task_c1 %>% knitr::kable()
```

<table>
<thead>
<tr class="header">
<th style="text-align: right;">port</th>
<th style="text-align: right;">mean</th>
<th style="text-align: right;">max</th>
<th style="text-align: right;">sum</th>
<th style="text-align: right;">Raz</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: right;">37</td>
<td style="text-align: right;">35089.99</td>
<td style="text-align: right;">209402</td>
<td style="text-align: right;">32136394510</td>
<td style="text-align: right;">174312</td>
</tr>
</tbody>
</table>

``` r
res_task_c2 <- res_task_c_data %>%
  filter(port == 37) %>%
  group_by(src) %>%
  summarise(mean = mean(bytes), .groups = "drop") %>%
  arrange(desc(mean)) %>%
  slice_head(n = 1) %>%
  select(src)

res_task_c2 %>% collect() %>% knitr::kable()
```

<table>
<thead>
<tr class="header">
<th style="text-align: left;">src</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: left;">14.31.107.42</td>
</tr>
</tbody>
</table>

## Оценка результата

Импортированы и проанализированы данные tm_data. Выполнены задания по
анализу утечек данных.

## Вывод

В ходе работы мы изучили возможности библиотеки Arrow, научились
анализировать сетевой трафик и применять облачные технологии для
обработки данных.
