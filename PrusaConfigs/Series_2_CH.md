# Печать с использованием подогрева камеры в PrusaSlicer для принтеров Picaso Series 2.

Для печати с подогревом камеры необходимо включить подогрев камеры в настройках принтера и создать отдельный профиль в PrusaSlicer, изменив Пользовательский G-code.

## Односопельная печать

Стартовый g-code:
```
G21 ;dimensions in millimeters
G90 ;absolute coordinate system
M104 T0 S120 ; преднагрев сопла
M140 S{first_layer_bed_temperature[0]} ; преднагрев стола
M141 S80 ; преднагрев камеры
G28
G0 F600 X180 Y180 Z80
M190 S{first_layer_bed_temperature[0]} ; греем стол
M191 S80 ; греем камеру
M109 T0 S{first_layer_temperature[0]} ; греем сопло
```

Необходимая температура камеры задаётся командами ```M141``` и ```M191```.

Для маленьких принтеров максимальная температура камеры: плюс 80 °С, для больших принтеров плюс 90 °С.

## Двухсопельная печать

Если требуется выбрать меньшую температуру стола, то добавляем сравнение температур стола.

Стартовый G-code:
```
{if first_layer_bed_temperature[1]>first_layer_bed_temperature[0]}M140 S{first_layer_bed_temperature[0]}{else}M140 S{first_layer_bed_temperature[1]}{endif} ; сравниваем температуры стола
M104 T0 S120
M104 T1 S120
M141 S80 ; преднагрев камеры
G28 ; home all axes
G0 F600 X180 Y180 Z80
{if first_layer_bed_temperature[1]>first_layer_bed_temperature[0]}M190 S{first_layer_bed_temperature[0]}{else}M190 S{first_layer_bed_temperature[1]}{endif} ; сравниваем температуры стола
M191 S80 ; греем камеру
M109 T0 S{first_layer_temperature[0]}
M109 T1 S{first_layer_temperature[1]}
```
