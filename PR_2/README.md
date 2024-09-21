# PR_2_2
Arikova Kristina
2024-09-21

# Практика 002

По дисциплине “Информационно-аналитические технологии поиска угроз
инорфмационной безопасности”

Основы обработки данных с помощью R и Dplyr

## Цель работы

1.  Развить практические навыки использования языка программирования R
    для обработки данных
2.  Закрепить знания базовых типов данных языка R
3.  Развить практические навыки использования функций обработки данных
    пакета dplyr – функции `select()`, `filter()`,`mutate()`,
    `arrange()`, `group_by()`

## Выполнение работы

### 1. Сколько строк в датафрейме?

``` r
library(dplyr)
```


    Присоединяю пакет: 'dplyr'

    Следующие объекты скрыты от 'package:stats':

        filter, lag

    Следующие объекты скрыты от 'package:base':

        intersect, setdiff, setequal, union

``` r
starwars
```

    # A tibble: 87 × 14
       name     height  mass hair_color skin_color eye_color birth_year sex   gender
       <chr>     <int> <dbl> <chr>      <chr>      <chr>          <dbl> <chr> <chr> 
     1 Luke Sk…    172    77 blond      fair       blue            19   male  mascu…
     2 C-3PO       167    75 <NA>       gold       yellow         112   none  mascu…
     3 R2-D2        96    32 <NA>       white, bl… red             33   none  mascu…
     4 Darth V…    202   136 none       white      yellow          41.9 male  mascu…
     5 Leia Or…    150    49 brown      light      brown           19   fema… femin…
     6 Owen La…    178   120 brown, gr… light      blue            52   male  mascu…
     7 Beru Wh…    165    75 brown      light      blue            47   fema… femin…
     8 R5-D4        97    32 <NA>       white, red red             NA   none  mascu…
     9 Biggs D…    183    84 black      light      brown           24   male  mascu…
    10 Obi-Wan…    182    77 auburn, w… fair       blue-gray       57   male  mascu…
    # ℹ 77 more rows
    # ℹ 5 more variables: homeworld <chr>, species <chr>, films <list>,
    #   vehicles <list>, starships <list>

### 1. Сколько строк в датафрейме?

``` r
starwars %>% nrow()
```

    [1] 87

### 2. Сколько столбцов в датафрейме?

``` r
starwars %>% ncol()
```

    [1] 14

### 3.Как просмотреть примерный вид датафрейма?

``` r
starwars %>% glimpse()
```

    Rows: 87
    Columns: 14
    $ name       <chr> "Luke Skywalker", "C-3PO", "R2-D2", "Darth Vader", "Leia Or…
    $ height     <int> 172, 167, 96, 202, 150, 178, 165, 97, 183, 182, 188, 180, 2…
    $ mass       <dbl> 77.0, 75.0, 32.0, 136.0, 49.0, 120.0, 75.0, 32.0, 84.0, 77.…
    $ hair_color <chr> "blond", NA, NA, "none", "brown", "brown, grey", "brown", N…
    $ skin_color <chr> "fair", "gold", "white, blue", "white", "light", "light", "…
    $ eye_color  <chr> "blue", "yellow", "red", "yellow", "brown", "blue", "blue",…
    $ birth_year <dbl> 19.0, 112.0, 33.0, 41.9, 19.0, 52.0, 47.0, NA, 24.0, 57.0, …
    $ sex        <chr> "male", "none", "none", "male", "female", "male", "female",…
    $ gender     <chr> "masculine", "masculine", "masculine", "masculine", "femini…
    $ homeworld  <chr> "Tatooine", "Tatooine", "Naboo", "Tatooine", "Alderaan", "T…
    $ species    <chr> "Human", "Droid", "Droid", "Human", "Human", "Human", "Huma…
    $ films      <list> <"A New Hope", "The Empire Strikes Back", "Return of the J…
    $ vehicles   <list> <"Snowspeeder", "Imperial Speeder Bike">, <>, <>, <>, "Imp…
    $ starships  <list> <"X-wing", "Imperial shuttle">, <>, <>, "TIE Advanced x1",…

### 4. Сколько уникальных рас персонажей (species) представлено в данных?

``` r
length(unique(starwars$species))
```

    [1] 38

### 5. Найти самого высокого персонажа.

``` r
starwars %>% arrange(desc(height)) %>% head(1) %>% select(name)
```

    # A tibble: 1 × 1
      name       
      <chr>      
    1 Yarael Poof

### 6. Найти всех персонажей ниже 170.

``` r
starwars %>% filter(height < 170)%>%
select(name)
```

    # A tibble: 22 × 1
       name                 
       <chr>                
     1 C-3PO                
     2 R2-D2                
     3 Leia Organa          
     4 Beru Whitesun Lars   
     5 R5-D4                
     6 Yoda                 
     7 Mon Mothma           
     8 Wicket Systri Warrick
     9 Nien Nunb            
    10 Watto                
    # ℹ 12 more rows

### 7. Подсчитать ИМТ (индекс массы тела) для всех персонажей.

``` r
starwars %>% mutate("BMI" = mass/(height*height)) %>% select(name,BMI)
```

    # A tibble: 87 × 2
       name                   BMI
       <chr>                <dbl>
     1 Luke Skywalker     0.00260
     2 C-3PO              0.00269
     3 R2-D2              0.00347
     4 Darth Vader        0.00333
     5 Leia Organa        0.00218
     6 Owen Lars          0.00379
     7 Beru Whitesun Lars 0.00275
     8 R5-D4              0.00340
     9 Biggs Darklighter  0.00251
    10 Obi-Wan Kenobi     0.00232
    # ℹ 77 more rows

### 8.Найти 10 самых “вытянутых” персонажей.

``` r
starwars %>% mutate(Stretching = mass/height)  %>% arrange(desc(Stretching)) %>% head(10) %>% select(name,Stretching) 
```

    # A tibble: 10 × 2
       name                  Stretching
       <chr>                      <dbl>
     1 Jabba Desilijic Tiure      7.76 
     2 Grievous                   0.736
     3 IG-88                      0.7  
     4 Owen Lars                  0.674
     5 Darth Vader                0.673
     6 Jek Tono Porkins           0.611
     7 Bossk                      0.595
     8 Tarfful                    0.581
     9 Dexter Jettster            0.515
    10 Chewbacca                  0.491

### 10. Найти средний возраст персонажей каждой расы вселенной Звездных войн.

### 10. Найти самый распространенный цвет глаз персонажей вселенной Звездных войн.

### 11. Подсчитать среднюю длину имени в каждой расе вселенной Звездных войн.

## Вывод

1.  Получены практические навыки использования языка программирования R
    для обработки данных
2.  Закреплены знания базовых типов данных языка R
3.  Получены практические навыки использования функций обработки данных
    пакета dplyr
