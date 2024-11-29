# Практика 005
Arikova Kristina
2024-11-05

Исследование информации о состоянии беспроводных сетей.

## Цель

1.  Получить знания о методах исследования радиоэлектронной обстановки.

2.  Составить представление о механизмах работы Wi-Fi сетей на канальном
    и сетевом уровне модели OSI.

3.  Зекрепить практические навыки использования языка программирования R
    для  
    обработки данных.

4.  Закрепить знания основных функций обработки данных экосистемы
    tidyverseязыка R.

## Исходные данные

-   Компьютер

-   ОС Windows

-   Rstudio

-   Библиотека readr

-   Данные P2_wifi_data, собранные с помощью анализатора беспроводного
    трафика airodump-ng

-   Github

## Общий план выполнения

1.  Подготовка данных

2.  Анализ точек доступа

3.  Анализ данных клиентов

## Содержание ЛР

### Шаг 1. Подготовка данных

Импортированы данные о беспроводном трафике.

``` r
Data <- read.csv('./P2_wifi_data.csv')
head(Data)
```

                  BSSID      First.time.seen       Last.time.seen channel Speed
    1 BE:F1:71:D5:17:8B  2023-07-28 09:13:03  2023-07-28 11:50:50       1   195
    2 6E:C7:EC:16:DA:1A  2023-07-28 09:13:03  2023-07-28 11:55:12       1   130
    3 9A:75:A8:B9:04:1E  2023-07-28 09:13:03  2023-07-28 11:53:31       1   360
    4 4A:EC:1E:DB:BF:95  2023-07-28 09:13:03  2023-07-28 11:04:01       7   360
    5 D2:6D:52:61:51:5D  2023-07-28 09:13:03  2023-07-28 10:30:19       6   130
    6 E8:28:C1:DC:B2:52  2023-07-28 09:13:03  2023-07-28 11:55:38       6   130
      Privacy Cipher Authentication Power X..beacons     X..IV           LAN.IP
    1    WPA2   CCMP            PSK   -30        846       504    0.  0.  0.  0
    2    WPA2   CCMP            PSK   -30        750       116    0.  0.  0.  0
    3    WPA2   CCMP            PSK   -68        694        26    0.  0.  0.  0
    4    WPA2   CCMP            PSK   -37        510        21    0.  0.  0.  0
    5    WPA2   CCMP            PSK   -57        647         6    0.  0.  0.  0
    6     OPN                         -63        251      3430  172. 17.203.197
      ID.length           ESSID Key
    1        12    C322U13 3965  NA
    2         4            Cnet  NA
    3         2              KC  NA
    4        14  POCO X5 Pro 5G  NA
    5        25                  NA
    6        13   MIREA_HOTSPOT  NA

Формат CSV лога airodump-ng меняется внутри файла (имеет разное число
столбцов). По сути, это два разных датасета:

-   датасет 1 – анонсы беспроводных точек доступа;

-   датасет 2 – запросы на подключение клиентов к известным им точкам
    доступа.  
    Разъеденим данные на два датасета.

``` r
library(tidyverse)
```

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
library(readr)
```

``` r
access_points <- read.csv('./P2_wifi_data.csv', nrows = 167)
head(access_points)
```

                  BSSID      First.time.seen       Last.time.seen channel Speed
    1 BE:F1:71:D5:17:8B  2023-07-28 09:13:03  2023-07-28 11:50:50       1   195
    2 6E:C7:EC:16:DA:1A  2023-07-28 09:13:03  2023-07-28 11:55:12       1   130
    3 9A:75:A8:B9:04:1E  2023-07-28 09:13:03  2023-07-28 11:53:31       1   360
    4 4A:EC:1E:DB:BF:95  2023-07-28 09:13:03  2023-07-28 11:04:01       7   360
    5 D2:6D:52:61:51:5D  2023-07-28 09:13:03  2023-07-28 10:30:19       6   130
    6 E8:28:C1:DC:B2:52  2023-07-28 09:13:03  2023-07-28 11:55:38       6   130
      Privacy Cipher Authentication Power X..beacons X..IV           LAN.IP
    1    WPA2   CCMP            PSK   -30        846   504    0.  0.  0.  0
    2    WPA2   CCMP            PSK   -30        750   116    0.  0.  0.  0
    3    WPA2   CCMP            PSK   -68        694    26    0.  0.  0.  0
    4    WPA2   CCMP            PSK   -37        510    21    0.  0.  0.  0
    5    WPA2   CCMP            PSK   -57        647     6    0.  0.  0.  0
    6     OPN                         -63        251  3430  172. 17.203.197
      ID.length           ESSID Key
    1        12    C322U13 3965  NA
    2         4            Cnet  NA
    3         2              KC  NA
    4        14  POCO X5 Pro 5G  NA
    5        25                  NA
    6        13   MIREA_HOTSPOT  NA

``` r
client_requests <- read.csv('./P2_wifi_data.csv', skip = 169)
head(client_requests)
```

            Station.MAC      First.time.seen       Last.time.seen Power X..packets
    1 CA:66:3B:8F:56:DD  2023-07-28 09:13:03  2023-07-28 10:59:44   -33        858
    2 96:35:2D:3D:85:E6  2023-07-28 09:13:03  2023-07-28 09:13:03   -65          4
    3 5C:3A:45:9E:1A:7B  2023-07-28 09:13:03  2023-07-28 11:51:54   -39        432
    4 C0:E4:34:D8:E7:E5  2023-07-28 09:13:03  2023-07-28 11:53:16   -61        958
    5 5E:8E:A6:5E:34:81  2023-07-28 09:13:04  2023-07-28 09:13:04   -53          1
    6 10:51:07:CB:33:E7  2023-07-28 09:13:05  2023-07-28 11:56:06   -43        344
                   BSSID Probed.ESSIDs
    1  BE:F1:71:D5:17:8B  C322U13 3965
    2  (not associated)   IT2 Wireless
    3  BE:F1:71:D6:10:D7  C322U21 0566
    4  BE:F1:71:D5:17:8B  C322U13 3965
    5  (not associated)               
    6  (not associated)               

Приведены датасеты в вид “аккуратных данных”, преобразованы типы
столбцов в соответствии с типом данных.

``` r
access_points <- access_points %>% 
  mutate_at(vars(BSSID, Privacy, Cipher, Authentication, LAN.IP, ESSID), trimws) %>%
  mutate_at(vars(BSSID, Privacy, Cipher, Authentication, LAN.IP, ESSID), na_if, "")

access_points$First.time.seen <- as.POSIXct(access_points$First.time.seen, format = "%Y-%m-%d %H:%M:%S")
access_points$Last.time.seen <- as.POSIXct(access_points$Last.time.seen, format = "%Y-%m-%d %H:%M:%S")

client_requests <- client_requests %>% 
  mutate_at(vars(Station.MAC, BSSID, Probed.ESSIDs), trimws) %>%
  mutate_at(vars(Station.MAC, BSSID, Probed.ESSIDs), na_if, "")

client_requests$First.time.seen <- as.POSIXct(client_requests$First.time.seen, format = "%Y-%m-%d %H:%M:%S")
client_requests$Last.time.seen <- as.POSIXct(client_requests$Last.time.seen, format = "%Y-%m-%d %H:%M:%S")
```

Общая структура данных по датасетам:  

``` r
glimpse(access_points)
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
    $ X..beacons      <int> 846, 750, 694, 510, 647, 251, 1647, 1251, 704, 617, 13…
    $ X..IV           <int> 504, 116, 26, 21, 6, 3430, 80, 11, 0, 0, 86, 0, 0, 0, …
    $ LAN.IP          <chr> "0.  0.  0.  0", "0.  0.  0.  0", "0.  0.  0.  0", "0.…
    $ ID.length       <int> 12, 4, 2, 14, 25, 13, 12, 13, 24, 12, 10, 0, 24, 24, 1…
    $ ESSID           <chr> "C322U13 3965", "Cnet", "KC", "POCO X5 Pro 5G", NA, "M…
    $ Key             <lgl> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA…

``` r
glimpse(client_requests)
```

    Rows: 12,269
    Columns: 7
    $ Station.MAC     <chr> "CA:66:3B:8F:56:DD", "96:35:2D:3D:85:E6", "5C:3A:45:9E…
    $ First.time.seen <dttm> 2023-07-28 09:13:03, 2023-07-28 09:13:03, 2023-07-28 …
    $ Last.time.seen  <dttm> 2023-07-28 10:59:44, 2023-07-28 09:13:03, 2023-07-28 …
    $ Power           <chr> " -33", " -65", " -39", " -61", " -53", " -43", " -31"…
    $ X..packets      <chr> "      858", "        4", "      432", "      958", " …
    $ BSSID           <chr> "BE:F1:71:D5:17:8B", "(not associated)", "BE:F1:71:D6:…
    $ Probed.ESSIDs   <chr> "C322U13 3965", "IT2 Wireless", "C322U21 0566", "C322U…

### Шаг 2. Анализ точек доступа

Найдены небезопасные точки доступа.

``` r
OpnPrivacy <- access_points %>% filter(Privacy == 'OPN') %>% select(BSSID)
OpnPrivacy = unique(OpnPrivacy)
head(OpnPrivacy)
```

                  BSSID
    1 E8:28:C1:DC:B2:52
    2 E8:28:C1:DC:B2:50
    3 E8:28:C1:DC:B2:51
    4 E8:28:C1:DC:FF:F2
    5 00:25:00:FF:94:73
    6 E8:28:C1:DD:04:52

Определены производители каждого обнаруженного устройства.

``` r
fabricator <- sapply(OpnPrivacy, function(mac) substr(mac, 1, 8)) %>% unique()

fabricator
```

          BSSID     
     [1,] "E8:28:C1"
     [2,] "00:25:00"
     [3,] "E0:D9:E3"
     [4,] "00:26:99"
     [5,] "02:BC:15"
     [6,] "00:AB:0A"
     [7,] "00:03:7A"
     [8,] "00:03:7F"
     [9,] "00:3E:1A"
    [10,] "02:67:F1"
    [11,] "02:CF:8B"
    [12,] "00:53:7A"

    E8:28:C1 - Eltex Enterprise Ltd.
    00:25:00 - Apple, Inc.
    E0:D9:E3 - Eltex Enterprise Ltd.
    00:26:99 - Cisco Systems, Inc
    00:03:7A - Taiyo Yuden Co., Ltd.
    00:03:7F - Atheros Communications, Inc.

Остальные производители не были найдены с помощью интренет-ресурса
[ouilookup.com](https://ouilookup.com/).

Выявлены устройства, использующие последнюю версию протокола шифрования
WPA3, и названия точек доступа, реализованных на этих устройствах.

``` r
access_points %>% filter(str_detect(access_points$Privacy, 'WPA3') == TRUE) %>% select(BSSID, ESSID)
```

                  BSSID              ESSID
    1 26:20:53:0C:98:E8               <NA>
    2 A2:FE:FF:B8:9B:C9         Christie’s
    3 96:FF:FC:91:EF:64               <NA>
    4 CE:48:E7:86:4E:33 iPhone (Анастасия)
    5 8E:1F:94:96:DA:FD iPhone (Анастасия)
    6 BE:FD:EF:18:92:44            Димасик
    7 3A:DA:00:F9:0C:02  iPhone XS Max 🦊🐱🦊
    8 76:C5:A0:70:08:96               <NA>

Отсортированы точки доступа по интервалу времени в минутах, в течение
которого они находились на связи, по убыванию.

``` r
access_points %>% mutate(duration = as.numeric(difftime(Last.time.seen, First.time.seen, units = "mins"))) %>% arrange(desc(duration)) %>% select(BSSID, duration) %>% head(10)
```

                   BSSID duration
    1  00:25:00:FF:94:73 163.2500
    2  E8:28:C1:DD:04:52 162.9333
    3  E8:28:C1:DC:B2:52 162.5833
    4  08:3A:2F:56:35:FE 162.4333
    5  6E:C7:EC:16:DA:1A 162.1500
    6  E8:28:C1:DC:B2:50 162.1000
    7  E8:28:C1:DC:B2:51 162.0833
    8  48:5B:39:F9:7A:48 162.0833
    9  E8:28:C1:DC:FF:F2 162.0667
    10 8E:55:4A:85:5B:01 162.0500

  
Составлен топ-10 самых быстрых точек доступа.

``` r
access_points %>% arrange(desc(Speed)) %>%  select(BSSID, Speed) %>% head(10)
```

                   BSSID Speed
    1  26:20:53:0C:98:E8   866
    2  96:FF:FC:91:EF:64   866
    3  CE:48:E7:86:4E:33   866
    4  8E:1F:94:96:DA:FD   866
    5  9A:75:A8:B9:04:1E   360
    6  4A:EC:1E:DB:BF:95   360
    7  56:C5:2B:9F:84:90   360
    8  E8:28:C1:DC:B2:41   360
    9  E8:28:C1:DC:B2:40   360
    10 E8:28:C1:DC:B2:42   360

Отсортированы точки доступа по частоте отправки запросов (beacons) в
единицу времени по их убыванию.

### Шаг 3. Анализ данных клиентов

Обределены прозводители каждого обраруженного устройства.

Обнаружены устройства, которые НЕ рандомизируют свой MAC адрес.

Кластеризованы запросы от устройств к точкам доступа по их именам.

  
Время появления устройства в зоне радиовидимости и время выхода его из
нее.

Определена стабильность уровня сигнала внури кластера во времени.
Выявлен наиболее стабильный кластер.

## Оценка результата

1.  Получены практические навыки использования языка программирования R
    для обработки данных
2.  Закреплены знания базовых типов данных языка R
3.  Получены практические навыки использования функций обработки данных
    пакета dplyr
4.  Закреплены знания основных функций обработки данных экосистемы
    tidyverseязыка R

## Вывод

В результате выполнения работы были закреплены умения работы с основными
инструментами обработки данных на языке R.
