<div style = "font-family: 'Open Sans', sans-serif; font-size: 16px">

# ModuleWS2812

<div style = "color: #555">
    <p align="center">
    <img src="./res/logo.png" width="400" title="hover text">
    </p>
</div>

## Лицензия

<div style = "color: #555">

В разработке
</div>

## Описание
<div style = "color: #555">

Модуль предназначен для работы с RGB лентами в рамках фреймворка EcoLite и обеспечивает следующий функционал:
- Инициализацию RGB-лент (матриц) в качестве актуаторов;
- Управление яркостью и цветом светодиодов.

Базовый функционал включает в себя следующие возможности:
- Задание цвета выбранного светодиода (с выборочным сохранением состояния остальных светодиодов);
- Задание одного цвета для всех светодиодов;
- Задание цвета для каждого светодиода за один раз.

Предусмотрено несколько способов задания цвета:
1. [R, G, B] массив. Например [255, 0, 0] - красный;
2. 6-разрядная hex запись. Например, '#ff0000' - красный.

Модуль разработан в соответствии с [архитектурой актуаторов](https://github.com/Konkery/ModuleActuator/blob/main/README.md), соответственно, *ClassRGBStrip* наследует и реализует является функционал *ClassMiddleActuator*, а прикладная работа с данным модулем выполняется через *ClassChannelActuator*, который обеспечивает унифицированный интерфейс.

</div>

## Конструктор
<div style = "color: #555">

Конструктор принимает данные из конфига. Пример ниже:
```json
"23": {
    "pins": ["D23"],
    "name": "WS2812",
    "length": 16,
    "article": "",
    "type": "actuator",
    "channelNames": ["color"],
    "typeInSignals": ["digital"],
    "quantityChannel": 1,
    "busTypes": [],
    "manufacturingData": {},
    "modules": ["ModuleWS2812.min.js"]
}
```

Для корректной работы модуля требуется указать в конфиге порт, на котором работает лента, а также количество светодиодов на ней. Порт обязан быть с аппаратной поддержкой SPI.

</div>

### Поля
<div style = "color: #555">

- <mark style="background-color: lightblue">_Length</mark> - количество светодиодов ленты (матрицы);
- <mark style="background-color: lightblue">_Values</mark> - массив размерности *_Length × 3*, задающий цвет каждого светодиода.

</div>

### Методы
<div style = "color: #555">

- <mark style="background-color: lightblue">On(_chNum, _val, _opts)</mark> - устанавливает цвет и яркость одного либо всех пикселей ленты;
- <mark style="background-color: lightblue">Off(_chNum, _opts)</mark> - выключает все пиксели на устройстве.

</div>

### Примеры
#### Точечное управление цветом пикселей
<div style = "color: #555">

```js
let strip = SensorManager.CreateDevice('23')[0];
// Выключение всех светодиодов
strip.Off();        
// Установка цвета для пикселей 0, 1, 2 в красный, зеленый и синий цвета, яркость 50%
strip.On(0.5, { ledNum: 0, color: [255, 0, 0] });
strip.On(0.5, { ledNum: 1, color: [0, 255, 0] });
strip.On(0.5, { ledNum: 2, color: [0, 0, 255] });

// Аналогично можно задавать цвет с помощью  hexadecimal нотации
// strip.On(0.5, { ledNum: 0, color: '#ff0000' });
// strip.On(0.5, { ledNum: 1, color: '#00ff00' });
// strip.On(0.5, { ledNum: 2, color: '#0000ff' });

setTimeout(() => {
    strip.Off({ saveState: true });       // saveState = true: цвета пикселей вернутся при следующем вызове On(...) 
}, 2000);

setTimeout(() => {
    strip.On(0.5);       // saveState = true: цвета пикселей вернутся при следующем вызове On(...) 
}, 4000);

```

<!-- <div align='center'>
    <img src='./res/example-1.png'>
</div> -->

</div>

#### Эксклюзивное включение светодиодов
<div style = "color: #555">

```js
let strip = SensorManager.CreateDevice('22')[0];
// Установка цвета для пикселей 0, 1, 2 в красный, зеленый и синий цвета, яркость 50%
// Включение каждого светодиода выключает все остальные
strip.On(0.5, { ledNum: 0, color: [255, 0, 0], exclusive: true });

setTimeout(() => {
    strip.On(0.5, { ledNum: 1, color: [0, 255, 0], exclusive: true });
}, 500);

setTimeout(() => {
    strip.On(0.5, { ledNum: 2, color: [0, 0, 255], exclusive: true });
}, 1000);

setTimeout(() => {
    strip.Off({ saveState: true });       // saveState = true: цвета пикселей вернутся при следующем вызове On(...) 
}, 2000);

setTimeout(() => {
    strip.On(0.5);       // saveState = true: цвета пикселей вернутся при следующем вызове On(...) 
}, 4000);

```

<!-- <div align='center'>
    <img src='./res/example-1.png'>
</div> -->

</div>

#### Задание цвета всех светодиодов матрицы
<div style = "color: #555">

```js
let strip = SensorManager.CreateDevice('22')[0];

strip.On(0.4, { color: [
    '#000000', '#ab4b6b', '#ff7e67', '#8b8e99', 
    '#ab4b6b', '#ab4b6b', '#6c648b', '#ab98a9', 
    '#ab4b6b', '#ab4b6b', '#ab4b6b', '#ab4b6b', 
    '#000000', '#ab4b6b', '#000000', '#ab4b6b']
});


```

<!-- <div align='center'>
    <img src='./res/example-1.png'>
</div> -->

</div>

#### Установка случайного цвета для всей ленты (матрицы)
<div style = "color: #555">

```js
let strip = SensorManager.CreateDevice('22')[0];
// Установка всех пикселей в один случайно выбранный цвет, яркость 45%
strip.On(0.45, { color: 'random' });  
```

<!-- <div align='center'>
    <img src='./res/example-1.png'>
</div> -->

</div>

#### Установка случайного цвета каждого пикселя 
<div style = "color: #555">

```js
let strip = SensorManager.CreateDevice('22')[0];
// Установка всех светодиодов в случайные цвета, яркость случайная
setInterval(() => {
    strip.On(Math.random(), { color: 'randomAll' });  
}, 3000);

```

<!-- <div align='center'>
    <img src='./res/example-1.png'>
</div> -->

</div>

### Зависимости
<div style = "color: #555">

</div>

</div>
