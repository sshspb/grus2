# SMS-термометр Журавли GRUS2

Задача: получать показания термометра дистанционно, посредством SMS-сообщений.

Берём на вооружение плату контроллера Arduino Nano, модем SIM800L и цифровые датчики температуры DS18B20.

## Внешний вид SMS-термометра

![appearance](images/grus2-exterior.jpg)

## Схема электрическая принципиальная
Блок питания выдаёт стабилизированные 5,1 вольт, которые, в целях защиты платы, через диод Шоттки 1N5817 поступают на контроллер Arduino Nano, и, в целях снижения напряжения питания до 4 вольт, через четыре последовательно соединённых диодов 1N5817 на модем SIM800L. Согласование логических уровней ТТЛ и КМОП микросхем выполнено на резисторном делителе.

![electric schematic diagram](images/grus2-scheme.png)

![arduino nano pins](images/arduino-nano-pins.png)

![sim800l pins](images/sim800l-pins.jpg)

## GSM модуль SIM800L

- https://simcom.ee/documents/SIM800L/SIM800L%28MT6261%29_Hardware%20Design_V1.01.pdf
 
- https://simcom.ee/documents/SIM800H/SIM800%20Series_AT%20Command%20Manual_V1.10.pdf

### SIM800L GSM Module Pinout

- `NET` is a pin where you can solder Helical Antenna provided along with the module.

- `VCC` supplies power for the module. This can be anywhere from 3.4V to 4.4 volts. An external power source like Li-Po battery or DC-DC buck converters rated 3.7V 2A would work.

- `RST` (Reset) is a hard reset pin. If you absolutely got the module in a bad space, pull this pin low for 100ms to perform a hard reset.

- `RxD` (Receiver) pin is used for serial communication.

- `TxD` (Transmitter) pin is used for serial communication.

- `GND` is the Ground Pin and needs to be connected to GND pin on the Arduino.

- `RING` pin acts as a Ring Indicator. It is basically the ‘interrupt’ out pin from the module. It is by default high and will pulse low for 120ms when a call is received. It can also be configured to pulse when an SMS is received.

- `DTR` pin activates/deactivates sleep mode. Pulling it HIGH will put module in sleep mode, disabling serial communication. Pulling it LOW will wake the module up.

- `MIC±` is a differential microphone input. The two microphone pins can be connected directly to these pins.

- `SPK±` is a differential speaker interface. The two pins of a speaker can be tied directly to these two pins.
 
### Электропитание

Диапазон электропитания SIM800L составляет от 3,4 В до 4,4 В. Рекомендуемое напряжение - 4,0 В. Источник питания должен обеспечивать ток до 2 А. Вход VBAT настоятельно рекомендуется шунтировать конденсатором с низким внутренним сопротивлением (low ESR, equivalent series resistance), например, 100 мкФ.

Ток потребления модуля SIM800L:
```
Power down mode    0.05 mA
Sleep mode         1    mA
Idle mode         20    mA
Voice call       200    mA
Data mode GPRS   400    mA
IMAX Peak current During Tx burst 2.0 A
```

### Функция сброса
 
SIM800L имеет контакт RESET, используемый для сброса модуля. Пользователь может замкнуть контакт RESET на землю, после чего модуль перезагрузится. 

Электрические характеристики контакта RESET: 
```
VIH min 2.4 V 
VIL max 0.6 V
Low power time 105 ms
```

### Последовательный порт

SIM800L предоставляет один асинхронный последовательный порт, который поддерживат скорости передачи между модулем (как DCE, оборудование передачи данных) и клиентом (DTE, оборудование терминала данных) 1200, 2400, 4800, 9600, 19200, 38400, 57600 and 115200 bps. Параметры последовательного порта клиента (DTE) должны быть установлены как 8N1 - 8 data bits, no parity and 1 stop bit. 

Электрические характеристики последовательного порта модуля SIM800L:
```
Symbol  Min  Typ  Max  Unit 
VIL    -0.3   -   0.7   V 
VIH     2.1   -   3.1   V 
VOL      -    -   0.4   V 
VOH     2.4  2.8   -    V 
```

Электрические характеристики 3,3 вольтовой логики:
```
Symbol  Min  Max  Unit 
VIL     0.0  0.8   V 
VIH     2.0  3.3   V 
VOL     0.0  0.4   V 
VOH     2.4  3.3   V 
```

Рекомендуемое соединение между модулем DCE (SIM800L) и DTE с уровнем логики 3,3 В:

![serialInterface](images/serialInterface.jpg)

Электрические характеристики стандартной 5 вольтовой TTL логики:
```
Symbol  Min  Max  Unit 
VIL     0.0  0.8   V 
VIH     2.0  5.0   V 
VOL     0.0  0.4   V 
VOH     2.4  5.0   V 
```

Логические уровни контроллера Arduino Nano (ATMega328):
```
Symbol  Min  Max  Unit 
VIL     0.0  1.5   V 
VIH     3.0  5.0   V 
VOL     0.0  0.9   V 
VOH     4.2  5.0   V 
```

![logic-levels](images/logicLevel.jpg)

## Подсоединение датчиков DS18B20

Назначение выводов цифрового измерителя температуры DS18B20

![interior installation](images/dallas18b20.jpg)

Датчики подсоединяем к SMS-термометру телефонным 4-х жильным кабелем. 

![interior installation](images/rj14.jpg)

Мы одну из вилок что на конце кабеля срезаем и на её место располагаем датчик DS18B20 по следующей раскладке:

```
Контакты    Контакты     Цвет 
RJ-14 6P4C  DS18B20      (прямой раскладки)  
--- 1 ---   не использ.  чёрный 
--- 2 ---   Data         красный 
--- 3 ---   GND          зелёный
--- 4 ---   +5V          жёлтый
```

## Корпус
Всю электронную начинку поместили в корпус G221 производства Gainta Industries Ltd.

![G221 body drawing](images/g221.jpg)
