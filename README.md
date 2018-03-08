[![QSY.BY](http://qsy.by/wp-content/uploads/2017/06/logo1.png)](http://qsy.by/)
# БЕГУЩАЯ СТРОКА. ARDUINO. MAX7219 И ПАРА ПРОВОДОВ. http://qsy.by/begushhaya-stroka-arduino-max7219-i-para-provodov/
* [ОПИСАНИЕ ПРОЕКТА](#chapter-0)
* [МОДУЛЬ СВЕТОДИОДНОЙ МАТРИЦЫ С МИКРОСХЕМОЙ MAX7219](#chapter-1)
* [ВЫВОД ПИКСЕЛЕЙ С ПОМОЩЬЮ БИБЛИОТЕКИ MAX72XXPANEL](#chapter-2)
* [ВЫВОД ТЕКСТА С ПОМОЩЬЮ БИБЛИОТЕКИ ADAFRUIT-GFX-LIBRARY](#chapter-3)
* [БЕГУЩАЯ СТРОКА НА MAX7219](#chapter-4)
* [ПРОГРАММИРОВАНИЕ БЕГУЩЕЙ СТРОКИ.](#chapter-5)

<a id="chapter-0"></a>
## ОПИСАНИЕ ПРОЕКТА
Привет Ребятушки. Сегодня запилим бегущую строку на светодиодных модулях MAX7219 и Arduino. Задача очень простая и не потребует от нас больших знаний в области электроники и программирования. Для начала предлагаю изучить немного теории по устройству светодиодной матрицы, принципу ее подключения и посмотреть видео результата к которому мы будем стремиться на протяжении всей статьи. 

[![LCD_Scrolling](https://github.com/eu4dgc/Arduino_MAX7219_Led_Scrolling_Text/blob/master/IMG/Max7219_led.gif)]

Светодиодная матрица — это графический индикатор, который можно использовать для вывода простых изображений, букв и цифр. Я не ставлю задачу подробно разобраться с устройством матричных индикаторов, однако стоит заметить, что по сути матрица состоит и 8х8 светодиодов. По сути все сводиться к динамической индикации. Основываясь на этом, понятно, что группировать несколько матриц вместе задача не из простых. На каждый новый ряд или колонку матриц, нужно добавлять новый сдвиговый регистр вместе с проводами и резисторами, а по-хорошему еще и микросхему ULN2003.
К счастью, инженеры давно уже разработали специализированные микросхемы для управления разного рода индикаторами. В этой статье мы рассмотрим матричный модуль с микросхемой MAX7219. Как станет понятно позже, работать с таким модулем одно удовольствие.

## МОДУЛЬ СВЕТОДИОДНОЙ МАТРИЦЫ С МИКРОСХЕМОЙ MAX7219
<a id="chapter-1"></a>
Модуль представляет из себя плату с микросхемой, необходимой для неё обвязкой и, собственно, матричным индикатором. Обычно индикатор не впаивают в плату, а вставляют в разъем. Это сделано для того, чтобы группу модулей можно было сначала закрепить на какой то поверхности винтами, а затем вставить в них матрицы.

[![LCD_Max](https://github.com/eu4dgc/Arduino_MAX7219_Led_Scrolling_Text/blob/master/IMG/matriz-led-MAX7219.png)]


У модуля есть пять выводов на каждой стороне. С одной стороны данные входят в модуль, с другой стороны данные выходят из модуля и передаются в следующий. Это позволяет соединять матрицы у цепочку.

Входной разъем / Выходной разъем :

VCC, GND — питание;
DIN — вход данных;
CS — выбор модуля (chip select);
CLK — синхроимпульс.
Работает модуль от напряжения 5 Вольт.

## ВЫВОД ПИКСЕЛЕЙ С ПОМОЩЬЮ БИБЛИОТЕКИ MAX72XXPANEL
<a id="chapter-2"></a>
Для управления микросхемой MAX7219 воспользуемся библиотекой Max72xxPanel. Скачать её можно по ссылкам в конце статьи.

Установим библиотеку и напишем небольшой код, который будет выводить на дисплей всего одну точку с координатами x=3 и y=4. Точка будет мигать с периодом 600 миллисекунд.
Как уже говорилось ранее, матричные модули с микросхемой MAX7219 можно легко объединять. Именно для этой цели в начале программы мы задаем количество матриц по-горизонтали и по-вертикали. В данном случае используется одна матрица, так что оба этих параметра будут равны 1.

Важно отметить, что после включения и выключения пикселей с помощью функции drawPixel, необходимо вызвать функцию write. Без функции write, пиксели не высветятся на матрице!

Теперь напишем код, который отобразит на матрице смайл. Смайл зашифруем с помощью массива из восьми байт. Каждый байт массива будет отвечать за строку матрицы, а каждый бит в байте за точку в строке.
[![LCD_Smile](https://github.com/eu4dgc/Arduino_MAX7219_Led_Scrolling_Text/blob/master/IMG/NSE-1069-1_8.jpg)]

Примечание. В библиотеке Max72xxPanel есть функция setRotation, которая задает ориентацию изображения на матрице. Например, если мы захотим повернуть смайл на 90 градусов, нужно будет сразу после вызова функции setIntensity вызвать setRotation с соответствующими аргументами:

matrix.setRotation( 0, 1 );

первый параметр — это индекс матрицы, в нашем случае он равен нулю; второй параметр — количество поворотов на 90 градусов.

## ВЫВОД ТЕКСТА С ПОМОЩЬЮ БИБЛИОТЕКИ ADAFRUIT-GFX-LIBRARY
<a id="chapter-3"></a>

Подобным же образом можно выводить на матрицу и любой другой символ, например, букву. Но чтобы иметь возможность отображать любую букву английского алфавита, нам необходимо будет определить в программе целых 26 восьмибайтных массива! Это очень муторно, и разумеется кто-то это уже сделал до нас.

В популярной библиотеке Adafruit-GFX-Library помимо функций для работы с графикой и текстом, имеется и база латинских букв в верхнем и нижнем регистрах, а также все знаки препинания и прочие служебные символы. Ссылка на библиотеку есть в конце статьи.

Отобразить символ на матрице можно с помощью функции drawChar.

drawChar( x, y, символ, цвет, фон, размер );

Первые два параметра функции отвечают за координаты верхнего левого угла символа. Третий параметр — это сам символ. Цвет символа в нашем случае будет равен 1 или HIGH, так как матрица двухцветная. Фон равен 0 или LOW. Последний параметр «размер» сделаем равным 1.

Напишем программу, которая будет по-очереди выводить на матрицу все буквы фразы: «HELLO WORLD!».

Примечание. В библиотеке Adafruit_GFX имеется множество функций для работы с графикой. Например, drawCircle( 3, 3, 2, HIGH ) начертит окружность с центром {3,3} и радиусом 2. Последний параметр — цвет, но в случае монохромной матрицы он равен 1 или HIGH. Функция drawLine( 0, 0, 3, 6, HIGH ) начертит отрезок между точками {0,0} и {3,6}.

## БЕГУЩАЯ СТРОКА НА MAX7219
<a id="chapter-4"></a>
Для реализации идеи потребуется совсем немного деталей:

 два светодиодных модуля, состоящих из четырёх матриц 8 на 8 пикселей;
 соединительные провода;
 плата Arduino Nano;

Схема

На печатной плате используемого светодиодного модуля расположено 4 матрицы размером 8 на 8 пикселей. Каждое светодиодное табло управляется микросхемой  MAX7219.

[![LCD_Sxema](https://github.com/eu4dgc/Arduino_MAX7219_Led_Scrolling_Text/blob/master/IMG/max7219_fr.JPG)]

MAX7219 представляет собой контроллер управления led-дисплеями, матрицами с общим катодом и дискретными светодиодами в количестве до 64 шт. Для более комфортного восприятия информации, выводимой на светодиодное табло, рекомендуется устанавливать несколько модулей. Для этого их объединяют в последовательно включенные группы, то есть выход первого модуля (out) подключают к входу второго модуля (in). Моя сборка состоит из двух модулей (16 матриц), длины которых вполне хватит для удобного прочтения целых предложений. При этом подключение сборки к Arduino производиться точно также как и к одиночному модулю.

## ПРОГРАММИРОВАНИЕ БЕГУЩЕЙ СТРОКИ.
<a id="chapter-5"></a>

Бегущая строка из Arduino и светодиодных модулей под управлением MAX7219 практически готова. Настало время перейти к заключающей, программной части.

Расписывать код не вижу смысла. Он и так хорошо прокомментирован. Однако есть некоторые особенности, о которых стоит упомянуть.

Примечание. Важно. Стандартная библиотека Adafruit_GFX изначально поддерживает только английские шрифты, поэтому ребята из России постарались и переписали библиотеку добавив русские шрифты и всякие вкусняшки. Все библиотеки и скетч доступны у меня на странице в GitHUB.

Кусок кода для работы с com-портом, нужен для того чтобы оперативно менять текст сообщения выводимого на светодиодный модуль. Однако он нужен нам не только для этого. В дальнейшем посредством этой функции мы свяжем наш Telegram Bot и бегущую строку на Arduino.
