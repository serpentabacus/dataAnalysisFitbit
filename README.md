# dataAnalysisFitbit
## Аналитика отчетности по активности тренировок групп на основании данных фитнес-браслетов
**_Цель:_** Создание автоматизированного отчета в виде PivotTable(Excel) при подключение к удаленному серверу, с выделением из ряда групп лучшей за данный период времени.

### Входные данные
Рассматривалась таблица сводных данных следующей структуры:
* ID спортсменa;
* Статусу активности;
* Дате ;
* Количество шагов;
* Пройденная дистанция;
* Количество этажей, на которые человек поднялся ;
* Количество калорий ;
* Количество активных калорий потраченных в период тренировки;
* Время тренировки;
* Группа, в которой участвует спортсмен;

Таблица организована как лист данных в Excel;

### Анализ данных и работа с пропущенными значениями
#### Работа спортивного трекера.

В режиме повседневной активности устройство отслеживает количество пройденных шагов(поле Шаги), время сна, ходьбу по лестнице(поле Этажи). Это возможно благодаря использованию встроенного акселерометра и различных разработанных Garmin алгоритмов, позволяющих определять, что действительно можно считать за шаг, а что просто «отвлекающий манёвр» типа мытья посуды. После пройденного расстояния(поле Дистанция) следует информация о затраченных калориях(поле Калории). Такой подсчёт происходит также благодаря использованию оптического датчика сердечного ритма. При этом применяется также комплексный подход расчёта общего количества калорий на день – _включая калории, сжигаемые в процессе основного обмена веществ, то есть необходимые для обеспечения процесса жизнедеятельности_. Помимо калорий рассчитываются и минуты интенсивной активности (поле Активные минуты). Этот показатель вычисляется из расчёта на рекомендованное количество минут «умеренной физической нагрузки» в неделю 5х30 минут, то есть 150 минут. _Данный показатель можно настроить вручную._

В основном все устройства имеют только один режим для работы во время тренировок: бег. Выбрать для отслеживания во время тренировок можно следующие данные: время, дистанция, калории(поле Активные калории), сердечный ритм (количество ударов в минуту) и тренировочная зона (определяемая по пульсу). 

Результат изучения работы фитнес браслетов: 
* поле _«Шаги»_ можно исключить из контрольного показателя, так как они подсчитывают кол-во шагов за весь день, а не за период тренировки ;
* поле _«Дистанция»_ так же как и поле _«Шаги»_ дает отчет за весь день; 
* поля _«Этажи»_ и _«Калории»_ аналогично двум предыдущим;

Для выбора оцениваемого критерия связи была проведена следующая работа:
* данные были сгруппированы по группам, как базовому признаку;
* по активности участников;
* сформированы корреляционные таблицы, для выявления межфакторных связей;

Данные не проверялись на нормальность, поэтому для оценки связи между ним был использован непараметрический ранговый критерий Спирмена. 

![the average for the group for the entire period](http://i9.pixs.ru/storage/5/4/9/1png_6938305_25292549.png)

![changes in average for dates](http://i12.pixs.ru/storage/7/8/4/2png_9555860_25292784.png)

![averages data for all group](http://i12.pixs.ru/storage/7/9/6/3png_5547286_25292796.png)

Поле _«Активные калории»_ получает данные о затраченных калориях во время тренировки, когда человек запускает на спортивном браслете режим тренировки. Данный показатель невозможно настроить вручную. Берем во внимание, что данные получены от адекватных людей, которые не включали режим тренировки просто так. Данные поля «Активные калории» можем принять как потенциально  контрольный показатель выбора самой активной группы спортсменов.

Поле _«Активные минуты»_ исключится из возможно-контрольного, так как данный показатель, возможно, настроить для каждого человека вручную.

**Вывод:** За независимые контрольный показатель тренировок спортивной группы принять данные поля «Активные калории».

### Заполнение пропущенных значений
В исходных данных пропущенные значения встречаются в 2-х вариантах:
- отсутствуют все числовые значения;
- отсутствуют только значения поля Floor;

Для первого случая принято строки удаляются, так как в Excel для обработки отсутствующих значений принято использовать методы заполнения нулями, средними или методом интерполяции. При заполнении нулями, мы ухудшаем показатели команды, на что мы  не имеет права. При заполнении средними или методом интерполяции, средние показатели команды не меняются, то есть при удалении строк конечный результат не изменится.

Для второго случая проверялось влияние поля Floor (там, где он есть изначально) на активные калории, ведь именно этому показателю мы решили судить, какая команда оказалась лучше. Мы построили следующий порядок действий:
1)	График зависимости активных калорий от шагов, при условии, что Floors равен нулю, тогда он никак не повлияет на активные калории:  

![graph of active calories from steps for Floors = 0](http://i12.pixs.ru/storage/8/4/1/4png_8419439_25292841.png)

2) Теперь построим такой же график по всем исходным данным, что у нас были:

![graph of active calories from steps for Floors = NA](http://i9.pixs.ru/storage/8/4/3/5png_1555149_25292843.png)

Как видим, графики не особо отличаются, то есть равны значения поля Floors нулю или же заданы, зависимость не меняется.

3)	Определим коэффициент корреляции Спирмена (_r_) по выборке из генеральной совокупности:

| Correlation method="spearman"     | Step        | Distance       | Floors | Calories | ActiveMinutes |
|:------:|:--------:|:--------:|:--------:|:--------:|:--------:|
| Active Calories  | 0,83      |0,84     | 0,38 | 0,88 | 0,6 |

Можно сделать вывод, что активные калории напрямую зависят от шагов и в меньшей степени от других факторов. При этом r поля Floors к активным калориям равен 0.38. 

Уравнение множественной регрессии: ActiveCalories ~ Steps + Floors

#### Расчет уравнения множественной регрессии:
Выборка:

![table of sample](http://i12.pixs.ru/storage/9/2/1/6png_2997314_25292921.png)

1) Оценка уравнения регрессии.

К матрице X(Steps Floors) с переменными Xj добавляем единичный столбец:

![table of steps and floors](http://i12.pixs.ru/storage/9/4/3/7png_8606358_25292943.png)

За Матрицу Y принимается столбец ActiveCalories
2) Транспонируем матрицу Х получаем Xt
3) Умножаем матрицы, (Xt * X)

| 40     | 354608        | 101       |
|:------:|:--------:|:--------:|
| 354608  | 3616518878      |968903     |
| 101   | 968903   |433    |

4) Умножаем матрицы, (Xt * Y)

| 40     | 
|:------:|
| 354608  | 
| 101   |

5) Находим обратную матрицу (Xt * X) - 1

| 0,199     | -1,80E-05        | -0,00688       |
|:------:|:--------:|:--------:|
| -1,80E-05  | 0      |-1,00E-06     |
| -0,00688   | -1,00E-06   |0,006    |

6) Определяем вектор оценок коэффициентов регрессии:

![table of regression](http://i12.pixs.ru/storage/9/9/0/8png_7355463_25292990.png)

Уравнение регрессии (оценка уравнения регрессии):			
**Y(ActiveCalories) = 164,114 + 0.106 X1(Steps) + 10.042 X2(Floors)**


На графиках наглядно видно, что при нулевых значениях Floors значения регрессионного уравнения особо не изменяются. Таким образом, можно сделать вывод, что пропущенными значения Floors можно пренебречь и прировнять их к 0.

### Аналитическая часть

Уравнение парной регрессии для ActiveCalories от Steps имеет вид:

**Y(ActiveCalories) = 175,629 + 0.108 X(Steps)**

Уравнение множественной регрессия для ActiveCalories от Steps и Floors имеет вид:

**Y(ActiveCalories) = 164,114 + 0.106 X1(Steps) + 10.042 X2(Floors)**

Составим расчетную таблицу по данным из нашей выборки:

| Исходные ActivityCalories    | По уравнению парной регрессии        | По уравнению множественной регрессии       |
|:------:|:--------:|:--------:|
| 692 |	847 |	826 |
| 732 |	879 |	908 |
| 810 |	891 |	920 |
| 822 |	949 |	967 |
| 853 |	967 |	995 |
| 855 |	976 |	953 |
| 864 |	999 |	1016 |
| 780 |	1153 |	1158 |
| 133 |	282 |	279 |
| 473 |	490 |	474 |
| 595 |	526 |	519 |
| 588 |	649 |	641 |
| 713 |	652 |	634 |
| 766 |	728 |	708 |
| 696 |	771 |	751 |
| 807 |	781 |	801 |
| 942 |	903 |	901 |
| 1187 |	987 |	1013 |
| 1424 |	1061 |	1087 |
| 1236 |	1073 |	1059 |
| 1081 |	1149 |	1133 |
| 1271 |	1259 |	1231 |
| 1965 |	1263 |	1276 |
| 1350 |	1398 |	1389 |
| 1592 |	1567 |	1575 |
| 1565 |	1807 |	1792 |
| 1644 |	1847 |	1832 |
| 1180 |	1151 |	1156 |
| 1085 |	1188 |	1172 |
| 1487 |	1281 |	1284 |
| 1465 |	1296 |	1298 |
| 1300 |	1309 |	1321 |
| 1416 |	1342 |	1324 |
| 1387 |	1378 |	1359 |
| 1620 |	1401 |	1402 |
| 1524 |	1445 |	1445 |
| 1446 |	1517 |	1587 |
| 1528 |	1523 | 	1522 |
| 1500 |	1633 |	1651 |
| 1807 |	1861 |	1825 |

Дисперсии выборок составленных по уравнению парной и уравнению множественной регрессий равны 140781.6 и 140343.1 соответственно, СКО – 374.62 и 374.21, средние значения – 1129.475 и 1129.6.

При использовании двухвыборочного теста Стьюдента значение p = 0.997 > 0.05, что позволяет нам отвергнуть нулевую гипотезу о том, что выборки в тесте разные. Корреляция Спирмана дала результат r = 0,998.

**Вывод:** исходя из этих данных, можно сказать, что значения поля Floors не влияют на значения ActiveCalories, поэтому было принято решение заполнить отсутствующие поля колонки Floors нулями.

### Клиент
Клиентская сторона- PivotTable в составе рабочей книги Excel.

Язык создания приложения: VBA

Реализация:  Создание макроса.

Функционал макроса: 
-	Создание удаленного подключения к серверу с БД с помощью ODBC подключения
-	С помощью SQL запроса делать выборку данных без пропущенных значений 
-	Создание сводной таблице на основание полученных данных 
-	Сортировка по убыванию, данной таблицы по сумме потраченных «Активных калорий» группами за весь период
-	Создание Гистограммы потраченных «Активных калорий» по группам 
-	Наличия фильтров по «Дате» и «User ID»
-	Вывод названия лучшей группы 
-	Иметь защищенный исходный код

### Сервер
Аппаратная часть представляет собой сервер под управлением ОС Linux, что предопределяет выбор подключения ODBC

Обработка всех запросов происходит в программе phpMyAdmin, что позволяет производить любые преобразования нашей  SQL БД.

Количество одновременных подключений - **100**

Заказчик получает отдельный кабинет для работы (добавлениея данных в таблицу, создание доп. БД и т.д.). Для входа в кабинет заказчику предоставляются Логин и Пароль.

Итоговый вид программы:

![view of programm](http://i12.pixs.ru/storage/0/7/4/11png_9857456_25293074.png)

