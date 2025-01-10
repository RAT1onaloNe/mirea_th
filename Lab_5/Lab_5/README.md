# Исследование информации о состоянии беспроводных сетей

rat1onal@yandex.ru

## Цель работы

1.  Получить знания о методах исследования радиоэлектронной обстановки.
2.  Составить представление о механизмах работы Wi-Fi сетей на канальном
    и сетевом уровне модели OSI.
3.  Зекрепить практические навыки использования языка программирования R
    для обработки данных
4.  Закрепить знания основных функций обработки данных экосистемы
    tidyverse языка R

## Исходные данные

1.  Программное обеспечение Windows 11
2.  Rstudio Desktop и пакет dplyr
3.  Интерпретатор языка R 4.1
4.  Тестовые данные p2_wifi_data.csv

## План

1.  Импортировать данные
2.  Привести датасеты в вид “аккуратных данных”, преобразовать типы
    столбцов в соответствии с типом данных
3.  Просмотреть общую структуру данных с помощью функции glimpse() и
    провести анализ

## Ход выполения работы

### Импорт данных

``` r
library(tidyverse)
```

    Warning: пакет 'tidyverse' был собран под R версии 4.4.2

    Warning: пакет 'ggplot2' был собран под R версии 4.4.2

    Warning: пакет 'tidyr' был собран под R версии 4.4.2

    Warning: пакет 'readr' был собран под R версии 4.4.2

    Warning: пакет 'purrr' был собран под R версии 4.4.2

    Warning: пакет 'forcats' был собран под R версии 4.4.2

    Warning: пакет 'lubridate' был собран под R версии 4.4.2

    ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ✔ dplyr     1.1.4     ✔ readr     2.1.5
    ✔ forcats   1.0.0     ✔ stringr   1.5.1
    ✔ ggplot2   3.5.1     ✔ tibble    3.2.1
    ✔ lubridate 1.9.3     ✔ tidyr     1.3.1
    ✔ purrr     1.0.2     
    ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ✖ dplyr::filter() masks stats::filter()
    ✖ dplyr::lag()    masks stats::lag()
    ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

``` r
library(dplyr)
library(readr)
ds1<-read.csv("P2_wifi_data.csv",nrows = 167)
ds2<-read.csv("P2_wifi_data.csv",skip = 169)
```

### Формирование “аккуратных данных”

``` r
ds1<-ds1%>%mutate_at(vars(BSSID,Privacy,Cipher,Authentication,LAN.IP,ESSID),trimws)%>%mutate_at(vars(BSSID,Privacy,Cipher,Authentication,LAN.IP,ESSID),na_if,"")
ds1$First.time.seen<-as.POSIXct(ds1$First.time.seen,format="%Y-%m-%d %H:%M:%S")
ds1$Last.time.seen<-as.POSIXct(ds1$Last.time.seen,format="%Y-%m-%d %H:%M:%S")

ds2<-ds2%>%mutate_at(vars(Station.MAC,BSSID,Probed.ESSIDs),trimws)%>%mutate_at(vars(Station.MAC,BSSID,Probed.ESSIDs),na_if,"")
ds2$First.time.seen<-as.POSIXct(ds2$First.time.seen,format = "%Y-%m-%d %H:%M:%S")
ds2$Last.time.seen<-as.POSIXct(ds2$Last.time.seen,format = "%Y-%m-%d %H:%M:%S")
```

### Просмотр общей структуры данных

``` r
glimpse(ds1)
```

    Rows: 167
    Columns: 15
    $ BSSID           <chr> "BE:F1:71:D5:17:8B", "6E:C7:EC:16:DA:1A", "9A:75:A8:B9…
    $ First.time.seen <dttm> 2023-07-28 09:13:03, 2023-07-28 09:13:03, 2023-07-28 …
    $ Last.time.seen  <dttm> 2023-07-28 11:50:50, 2023-07-28 11:55:12, 2023-07-28 …
    $ channel         <int> 1, 1, 1, 7, 6, 6, 11, 11, 11, 1, 6, 14, 11, 11, 6, 6, …
    $ Speed           <int> 195, 130, 360, 360, 130, 130, 195, 130, 130, 195, 180,…
    $ Privacy         <chr> "WPA2", "WPA2", "WPA2", "WPA2", "WPA2", "OPN", "WPA2",…
    $ Cipher          <chr> "CCMP", "CCMP", "CCMP", "CCMP", "CCMP", NA, "CCMP", "C…
    $ Authentication  <chr> "PSK", "PSK", "PSK", "PSK", "PSK", NA, "PSK", "PSK", "…
    $ Power           <int> -30, -30, -68, -37, -57, -63, -27, -38, -38, -66, -42,…
    $ beacons         <int> 846, 750, 694, 510, 647, 251, 1647, 1251, 704, 617, 13…
    $ IV              <int> 504, 116, 26, 21, 6, 3430, 80, 11, 0, 0, 86, 0, 0, 0, …
    $ LAN.IP          <chr> "0.  0.  0.  0", "0.  0.  0.  0", "0.  0.  0.  0", "0.…
    $ ID.length       <int> 12, 4, 2, 14, 25, 13, 12, 13, 24, 12, 10, 0, 24, 24, 1…
    $ ESSID           <chr> "C322U13 3965", "Cnet", "KC", "POCO X5 Pro 5G", NA, "M…
    $ Key             <lgl> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA…

``` r
glimpse(ds2)
```

    Rows: 12,269
    Columns: 7
    $ Station.MAC     <chr> "CA:66:3B:8F:56:DD", "96:35:2D:3D:85:E6", "5C:3A:45:9E…
    $ First.time.seen <dttm> 2023-07-28 09:13:03, 2023-07-28 09:13:03, 2023-07-28 …
    $ Last.time.seen  <dttm> 2023-07-28 10:59:44, 2023-07-28 09:13:03, 2023-07-28 …
    $ Power           <chr> " -33", " -65", " -39", " -61", " -53", " -43", " -31"…
    $ packets         <chr> "      858", "        4", "      432", "      958", " …
    $ BSSID           <chr> "BE:F1:71:D5:17:8B", "(not associated)", "BE:F1:71:D6:…
    $ Probed.ESSIDs   <chr> "C322U13 3965", "IT2 Wireless", "C322U21 0566", "C322U…

## Анализ. Точки доступа.

### Определить небезопасные точки доступа (без шифрования – OPN)

``` r
unsafe_points<-ds1 %>%filter(Privacy=='OPN')%>%select(BSSID)%>%unique()
unsafe_points%>%select(BSSID)
```

                   BSSID
    1  E8:28:C1:DC:B2:52
    2  E8:28:C1:DC:B2:50
    3  E8:28:C1:DC:B2:51
    4  E8:28:C1:DC:FF:F2
    5  00:25:00:FF:94:73
    6  E8:28:C1:DD:04:52
    7  E8:28:C1:DE:74:31
    8  E8:28:C1:DE:74:32
    9  E8:28:C1:DC:C8:32
    10 E8:28:C1:DD:04:50
    11 E8:28:C1:DD:04:51
    12 E8:28:C1:DC:C8:30
    13 E8:28:C1:DE:74:30
    14 E0:D9:E3:48:FF:D2
    15 E8:28:C1:DC:B2:41
    16 E8:28:C1:DC:B2:40
    17 00:26:99:F2:7A:E0
    18 E8:28:C1:DC:B2:42
    19 E8:28:C1:DD:04:40
    20 E8:28:C1:DD:04:41
    21 E8:28:C1:DE:47:D2
    22 02:BC:15:7E:D5:DC
    23 E8:28:C1:DC:C6:B1
    24 E8:28:C1:DD:04:42
    25 E8:28:C1:DC:C8:31
    26 E8:28:C1:DE:47:D1
    27 00:AB:0A:00:10:10
    28 E8:28:C1:DC:C6:B0
    29 E8:28:C1:DC:C6:B2
    30 E8:28:C1:DC:BD:50
    31 E8:28:C1:DC:0B:B2
    32 E8:28:C1:DC:33:12
    33 00:03:7A:1A:03:56
    34 00:03:7F:12:34:56
    35 00:3E:1A:5D:14:45
    36 E0:D9:E3:49:00:B1
    37 E8:28:C1:DC:BD:52
    38 00:26:99:F2:7A:EF
    39 02:67:F1:B0:6C:98
    40 02:CF:8B:87:B4:F9
    41 00:53:7A:99:98:56
    42 E8:28:C1:DE:47:D0

### Определить производителя для каждого обнаруженного устройства

``` r
name<-sapply(unsafe_points,function(i) substr(i, 1, 8))%>%unique()
cat(name,"\n")
```

    E8:28:C1 00:25:00 E0:D9:E3 00:26:99 02:BC:15 00:AB:0A 00:03:7A 00:03:7F 00:3E:1A 02:67:F1 02:CF:8B 00:53:7A 

``` r
print('Eltex Enterprise Ltd; Apple, Inc; Eltex Enterprise Ltd; Cisco Systems, Inc; Taiyo Yuden Co., Ltd; Atheros Communications, Inc')
```

    [1] "Eltex Enterprise Ltd; Apple, Inc; Eltex Enterprise Ltd; Cisco Systems, Inc; Taiyo Yuden Co., Ltd; Atheros Communications, Inc"

### Выявить устройства, использующие последнюю версию протокола шифрования WPA3, и названия точек доступа, реализованных на этих устройствах

``` r
ds1%>%filter(grepl("WPA3", Privacy))%>%select(BSSID,ESSID,Privacy)
```

                  BSSID              ESSID   Privacy
    1 26:20:53:0C:98:E8               <NA> WPA3 WPA2
    2 A2:FE:FF:B8:9B:C9         Christie’s WPA3 WPA2
    3 96:FF:FC:91:EF:64               <NA> WPA3 WPA2
    4 CE:48:E7:86:4E:33 iPhone (Анастасия) WPA3 WPA2
    5 8E:1F:94:96:DA:FD iPhone (Анастасия) WPA3 WPA2
    6 BE:FD:EF:18:92:44            Димасик WPA3 WPA2
    7 3A:DA:00:F9:0C:02  iPhone XS Max 🦊🐱🦊 WPA3 WPA2
    8 76:C5:A0:70:08:96               <NA> WPA3 WPA2

### Отсортировать точки доступа по интервалу времени, в течение которого они находились на связи, по убыванию.

``` r
ds1%>%mutate(time=difftime(Last.time.seen,First.time.seen))%>%arrange(desc(time))%>%select(BSSID,time)%>%head(10)
```

                   BSSID      time
    1  00:25:00:FF:94:73 9795 secs
    2  E8:28:C1:DD:04:52 9776 secs
    3  E8:28:C1:DC:B2:52 9755 secs
    4  08:3A:2F:56:35:FE 9746 secs
    5  6E:C7:EC:16:DA:1A 9729 secs
    6  E8:28:C1:DC:B2:50 9726 secs
    7  E8:28:C1:DC:B2:51 9725 secs
    8  48:5B:39:F9:7A:48 9725 secs
    9  E8:28:C1:DC:FF:F2 9724 secs
    10 8E:55:4A:85:5B:01 9723 secs

### Обнаружить топ-10 самых быстрых точек доступа.

``` r
ds1%>%arrange(desc(Speed))%>%head(10)
```

                   BSSID     First.time.seen      Last.time.seen channel Speed
    1  26:20:53:0C:98:E8 2023-07-28 09:15:45 2023-07-28 09:33:10      44   866
    2  96:FF:FC:91:EF:64 2023-07-28 09:52:54 2023-07-28 10:25:02      44   866
    3  CE:48:E7:86:4E:33 2023-07-28 09:59:20 2023-07-28 10:04:15      44   866
    4  8E:1F:94:96:DA:FD 2023-07-28 10:08:32 2023-07-28 10:15:27      44   866
    5  9A:75:A8:B9:04:1E 2023-07-28 09:13:03 2023-07-28 11:53:31       1   360
    6  4A:EC:1E:DB:BF:95 2023-07-28 09:13:03 2023-07-28 11:04:01       7   360
    7  56:C5:2B:9F:84:90 2023-07-28 09:17:49 2023-07-28 10:27:22       1   360
    8  E8:28:C1:DC:B2:41 2023-07-28 09:18:16 2023-07-28 11:36:43      48   360
    9  E8:28:C1:DC:B2:40 2023-07-28 09:18:16 2023-07-28 11:51:48      48   360
    10 E8:28:C1:DC:B2:42 2023-07-28 09:18:30 2023-07-28 11:43:23      48   360
         Privacy Cipher Authentication Power beacons IV          LAN.IP ID.length
    1  WPA3 WPA2   CCMP        SAE PSK   -85       3  0   0.  0.  0.  0        10
    2  WPA3 WPA2   CCMP        SAE PSK   -85       9  0   0.  0.  0.  0        10
    3  WPA3 WPA2   CCMP        SAE PSK   -65       9  0   0.  0.  0.  0        27
    4  WPA3 WPA2   CCMP        SAE PSK   -67      12  0   0.  0.  0.  0        27
    5       WPA2   CCMP            PSK   -68     694 26   0.  0.  0.  0         2
    6       WPA2   CCMP            PSK   -37     510 21   0.  0.  0.  0        14
    7       WPA2   CCMP            PSK   -64     317 31   0.  0.  0.  0        10
    8        OPN   <NA>           <NA>   -89       5 23 169.254.175.203        12
    9        OPN   <NA>           <NA>   -88       5 89 172. 17.203.197        13
    10       OPN   <NA>           <NA>   -87       5  0   0.  0.  0.  0         0
                    ESSID Key
    1                <NA>  NA
    2                <NA>  NA
    3  iPhone (Анастасия)  NA
    4  iPhone (Анастасия)  NA
    5                  KC  NA
    6      POCO X5 Pro 5G  NA
    7          OnePlus 6T  NA
    8        MIREA_GUESTS  NA
    9       MIREA_HOTSPOT  NA
    10               <NA>  NA

### Отсортировать точки доступа по частоте отправки запросов (beacons) в единицу времени по их убыванию.

``` r
bb<-ds1%>%mutate(bb=beacons/as.numeric(difftime(Last.time.seen,First.time.seen)))%>%filter(!is.infinite(bb))%>%arrange(desc(bb))
bb%>%select(BSSID,bb)%>%head(10)
```

                   BSSID        bb
    1  F2:30:AB:E9:03:ED 0.8571429
    2  B2:CF:C0:00:4A:60 0.8000000
    3  3A:DA:00:F9:0C:02 0.5555556
    4  02:BC:15:7E:D5:DC 0.5000000
    5  00:3E:1A:5D:14:45 0.5000000
    6  76:C5:A0:70:08:96 0.5000000
    7  D2:25:91:F6:6C:D8 0.3846154
    8  BE:F1:71:D6:10:D7 0.1740831
    9  00:03:7A:1A:03:56 0.1666667
    10 38:1A:52:0D:84:D7 0.1630007

## Данные клиентов

### Определить производителя для каждого обнаруженного устройства

``` r
name<-ds2%>%filter(BSSID!='(not associated)')%>%filter(!is.na(BSSID))%>%mutate(n=substr(BSSID,1,8))%>%select(n)
unique(name)
```

               n
    1   BE:F1:71
    4   1E:93:E3
    5   E8:28:C1
    6   00:25:00
    7   00:26:99
    8   0C:80:63
    10  0A:C5:E1
    12  9A:75:A8
    13  8A:A3:03
    14  4A:EC:1E
    16  08:3A:2F
    17  6E:C7:EC
    21  2A:E8:A2
    28  56:C5:2B
    30  9A:9F:06
    31  12:48:F9
    35  AA:F4:3F
    37  3A:70:96
    42  8E:55:4A
    43  5E:C7:C0
    44  E2:37:BF
    48  96:FF:FC
    50  CE:B3:FF
    58  76:70:AF
    60  00:AB:0A
    64  MIREA_HO
    66  8E:1F:94
    78  EA:7B:9B
    79  BE:FD:EF
    81  7E:3A:10
    83  00:23:EB
    87  E0:D9:E3
    88  3A:DA:00
    100 92:F5:7B
    103 AndroidS
    104 DC:09:4C
    110 22:C9:7F
    112 TP-Link_
    117 92:12:38
    120 B2:1B:0C
    134 1E:C2:8E
    136 A2:64:E8
    138 A6:02:B9
    150 AE:3E:7F
    158 B6:C4:55
    161 86:DF:BF
    163 02:67:F1
    169 36:46:53
    176 82:CD:7D
    182 00:03:7F
    183 00:0D:97

``` r
print('00:23:EB-Cisco Systems, Inc;
00:25:00-Apple, Inc.;
00:26:99-Cisco Systems, Inc;
00:03:7F-Atheros Communications, Inc.;
00:0D:97-Hitachi Energy USA Inc.;
0C:80:63-Tp-Link Technologies Co.,Ltd.;
DC:09:4C-Huawei Technologies Co.,Ltd;
08:3A:2F-Guangzhou Juan Intelligent Tech Joint Stock Co.,Ltd;
E0:D9:E3 E8:28:C1-Eltex Enterprise Ltd.;')
```

    [1] "00:23:EB-Cisco Systems, Inc;\n00:25:00-Apple, Inc.;\n00:26:99-Cisco Systems, Inc;\n00:03:7F-Atheros Communications, Inc.;\n00:0D:97-Hitachi Energy USA Inc.;\n0C:80:63-Tp-Link Technologies Co.,Ltd.;\nDC:09:4C-Huawei Technologies Co.,Ltd;\n08:3A:2F-Guangzhou Juan Intelligent Tech Joint Stock Co.,Ltd;\nE0:D9:E3 E8:28:C1-Eltex Enterprise Ltd.;"

### Обнаружить устройства, которые НЕ рандомизируют свой MAC адрес

``` r
norand<-ds2%>%filter(!grepl("^02|^06|^0A|^0E", BSSID))%>%filter(BSSID!='(not associated)')
norand %>% select(BSSID)%>%head(10)
```

                   BSSID
    1  BE:F1:71:D5:17:8B
    2  BE:F1:71:D6:10:D7
    3  BE:F1:71:D5:17:8B
    4  1E:93:E3:1B:3C:F4
    5  E8:28:C1:DC:FF:F2
    6  00:25:00:FF:94:73
    7  00:26:99:F2:7A:E2
    8  0C:80:63:A9:6E:EE
    9  E8:28:C1:DD:04:52
    10 1E:93:E3:1B:3C:F4

### Кластеризовать запросы от устройств к точкам доступа по их именам. Определить время появления устройства в зоне радиовидимости и время выхода его из нее.

``` r
ds2%>%group_by(Probed.ESSIDs)%>%summarise(
fseen=min(First.time.seen),lseen=max(Last.time.seen))%>%select(Probed.ESSIDs,fseen,lseen)
```

    # A tibble: 108 × 3
       Probed.ESSIDs                fseen               lseen              
       <chr>                        <dttm>              <dttm>             
     1 -D-13-                       2023-07-28 09:14:42 2023-07-28 10:26:42
     2 1                            2023-07-28 10:36:12 2023-07-28 11:56:13
     3 107                          2023-07-28 10:29:43 2023-07-28 10:29:43
     4 531                          2023-07-28 10:57:04 2023-07-28 10:57:04
     5 AAAAAOB/CC0ADwGkRedmi 3S     2023-07-28 09:34:20 2023-07-28 11:44:40
     6 AKADO-D967                   2023-07-28 10:31:55 2023-07-28 10:31:55
     7 AQAAAB6zaIoATwEURedmi Note 5 2023-07-28 10:25:19 2023-07-28 11:51:48
     8 ASUS                         2023-07-28 10:31:13 2023-07-28 10:31:13
     9 Alex-net2                    2023-07-28 10:01:06 2023-07-28 10:01:06
    10 AndroidAP177B                2023-07-28 09:13:09 2023-07-28 11:34:42
    # ℹ 98 more rows

### Оценить стабильность уровня сигнала внури кластера во времени. Выявить наиболее стабильный кластер.

``` r
ds2%>%mutate(t=difftime(Last.time.seen, First.time.seen))%>%filter(t!=0)%>%arrange(desc(t))%>%filter(!is.na(Probed.ESSIDs))%>%group_by(Probed.ESSIDs)%>%summarise(m=mean(t),Sd=sd(t))%>%filter(Sd!=0)%>%arrange(Sd)%>%select(Probed.ESSIDs,m,Sd)%>%head(1)
```

    # A tibble: 1 × 3
      Probed.ESSIDs m            Sd
      <chr>         <drtn>    <dbl>
    1 nvripcsuite   9780 secs  3.46

## Оценка результата

Был проведен анализ датасета с использованием библиотек
dplyr,tidyverse,reader,выполнены задания и сформирован отчет

## Вывод

Были получены знания о методах исследования радиоэлектронной обстановки,
получено представление о механизмах работы WIFi сетей на канальном и
сетевом уровне модели OSI. Закреплены навыки использования языка R.
Закреплены знания основных функций обработки данных экосистемы
tidyverse.