# fNIRS

В данной работе осуществляется построение классификатора данных, полученных методом спектроскопии в околоинфракрасном диапазоне (fNIRS) в результате выполнения когнитивной задачи.

Спектроскопия в околоинфракрасном диапазоне или оптическая томография (fNIRS)  - новый метод функциональной нейровизуализации. Методика схожа с функциональной МРТ, однако с помощью fNIRS возможно оценивать  активность коры не внутри томографа, а в лаборатории и даже на улице во время двигательной активности или повседневных дел пациента. Помимо этого, стоимость системы для fNIRS значительно ниже, чем фМРТ сканера. Система для fNIRS производит световое излучение в околоинфракрасном диапазоне в виде двух волн разной длины, благодаря которым можно распознать два состояния оксигенации гемоглобина. Источники и детекторы излучения устанавливаются в специальную шапочку. Система для fNIRS оценивает уровень окси- и дезоксигемоглобина, что позволяет оценить ативность всех областей коры головного мозга в режиме реального времени.

### Особенности метода fNIRS:


- Безопасность:
fNIRS не использует никакого вредного излучения, метод можно использовать у детей
- Малоинвазивная методика:
Для fNIRS не требуется дополнительного контрастного вещества
- Функциональный метод визуализации:
Контраст изображения достигается благодаря функционированию ткани, а не её анатомическим особенностям
- Доступность:
fNIRS - это относительно недорогая и простая в обращении технология
- Временное разрешение:
Временное разрешение у fNIRS выше, чем у фМРТ
- Пространственное разрешение:
Пространственное разрешение у fNIRS выше, чем у ЭЭГ
- Глубина проникновения света в ткани:
Глубина проникновения света в ткани (в головном мозге примерно 3 см) ограничивает размер исследуемого объекта

*(инфорация получена с сайта Центра нейроэкономики и когнитивных исследований (ВШЭ) (https://www.hse.ru/cdm-centre/fnirs/)*

При выполнении какого-либо действия требуется верифицировать процесс принятия решения.
Для данной работы в качестве оценки когнитивной нагрузки выбрана задача n-back.

Основной принцип n-back задачи: испытуемоу один за другим предъявляется ряд образов (в нашем случае визуальный образ в виде изображения с цифрой). При этом человек должен определить и указать, встречался ли предъявляемый образ 1 позицию назад (задача 1-назад), или 2 позиции назад (задача 2-назад), или 3 позиции назад (задача 3-назад), и так далее.

<h1 align="center"> <img src=https://user-images.githubusercontent.com/25271759/222893995-3f40b1d7-dcb6-46fa-a3d2-c91ac5f2c049.jpeg width="500"  align="center">

### Данные

Данны получены из открытого исследования проведенного в университете Тафтса по построению мозг-компьютерного интерфейса на баз FNIRS:

https://tufts.app.box.com/s/1e0831syu1evlmk9zx2pukpl3i32md6r

В качестве записи сигнала использовалось следующее оборудование: 

<h1 align="center"> <img width="600" alt="image" src="https://user-images.githubusercontent.com/25271759/222960983-3f2a5569-c8af-47d7-863b-a09c5fa55078.png"> </h1>

Объем базы 68 испытуемых:
- 62 испытуемых для тренировочной выборки
- 6 испытуемых для тестовой выборки

Загруженный датасет выглядит следующим образом:
<h1 align="center"> <img width="664" alt="image" src="https://user-images.githubusercontent.com/25271759/222961107-5c4b0954-2e65-4d83-8b37-c5914171fad9.png"> </h1>

На каждом временном шаге есть 8 численных измерений, объединяющих 2 пространственных положения (AB и CD), 2 концентрации (насыщенный кислородом и деоксигенированный гемоглобин) и 2 типа оптических данных (интенсивность и фаза). 

Проведен парсинг данных.
1 испытуемый:
16 задач n-back (0, 1, 2, 3-nback)
6816 – измерений
426 – измерений на 1 задачу
80 – секунд на 1 задачу
примерно 6 измерений на 1 секунду

62 испытуемых выбрано в качестве обучающего датасета - 422 592 измерения
6 испыетуемых выбраны в качестве тестового датасета – 40 896 измерений

Модель
==========

Написана функция разделения датасета на подвыборки длиной 6 измерений (примерно 1 секунда исследования), с перемешиванием.
- xTrain – данные AB_I_O, AB_PHI_O, AB_I_DO, AB_PHI_DO, CD_I_O, CD_PHI_O, CD_I_DO, CD_PHI_DO
- yTrain – данные label

<h1 align="center"> <img width="271" alt="image" src="https://user-images.githubusercontent.com/25271759/222963679-327318b9-66ba-4f0d-b157-26bdd1ba3d4d.png"> </h1>

<b>Полносвязная нейросеть.</b>
Первоначально была полносвязная нейросеть для датасета на одного испытуемого
Использовались разные батчи 32, 64, 128
Активационные функции linear на полносвязных слоях
Финальная активационная функция softmax

Итог: 
Точность на обучающей выборке 48,42%
Точность 50,88%
Точность 47,59%

Вывод: низкая точность, данная «концепция» была тестовой

<h1 align="center"> <img width="420" alt="image" src="https://user-images.githubusercontent.com/25271759/222963725-28b75a02-2191-4488-b576-781c68361495.png"> </h1>

<b>Сверточная нейросеть.</b>

Была проведена разработка и тестирование сверточной нейросети для датасета на одного испытуемого
Использовались разные батчи 32, 64, 128
Итог:

- Точность на обучающей выборке 26,37%
- Точность на валидационной выборке 27,91%
- Точность на тестовой выборке 72,01%

Вывод: результат лучше, чем на полносвязной сети, но опять же данная концепция является «тестовой»

<h1 align="center"> <img width="404" alt="image" src="https://user-images.githubusercontent.com/25271759/222963778-6d26ecfb-108b-4303-8a5f-c8023b53f252.png"> </h1>

<b>Рекурентная нейросеть (вариант 1).</b>
Разработка рекуррентной нейросети для всех испытуемых (62 человек, 6 испытуемый для тестовой выборки) на одного испытуемого
Использовались разные батчи 32, 64, 128 и различная скорость обучения в общей сложности на 350 эпохах
Активационные функции relu на полносвязных слоях
Финальная активационная функция softmax
Итог:

- Точность на обучающей выборке 96,89%
- Точность на валидационной выборке 54,03%
- Точность на тестовой выборке 94,03%

Вывод: результат хороший, но с точки зрения практики, не совсем эффективный 

<h1 align="center"> <img width="343" alt="image" src="https://user-images.githubusercontent.com/25271759/222969132-de1e9c23-c5fe-4082-93aa-c22108f31ea7.png"> </h1>

<b>Рекурентная нейросеть (вариант 2).</b>
Разработка рекуррентной нейросети для всех испытуемых (62 человек, 6 испытуемый для тестовой выборки) на одного испытуемого
Использовались разные батчи 32, 64, 128 в общей сложности на 350 эпохах
Активационные функции elu на полносвязных слоях
Финальная активационная функция softmax
Итог:

- Точность на обучающей выборке 94,35%
- Точность на валидационной выборке 53,93%
- Точность на тестовой выборке 95,31%

Вывод: результат хороший, но с точки зрения практики, не совсем эффективный 

<h1 align="center"> <img width="343" alt="image" src="https://user-images.githubusercontent.com/25271759/222965199-3ea2e743-fd2c-4891-bbbd-8f98d5d149d3.png"> </h1>

<b>Рекурентная нейросеть (вариант 3).</b>
Разработка рекуррентной нейросети для всех испытуемых (67 человек, 1 испытуемый для тестовой выборки) на одного испытуемого
Использовались разные батчи 32, 64, 128 в общей сложности на 500 эпохах
Активационные функции relu на полносвязных слоях
Финальная активационная функция softmax

Итог:

- Точность на обучающей выборке 77,38% на 67 испытуемых
- Точность на валидационной выборке 53,09% на 67 испытуемых
- Точность на тестовой выборке 27,02% на 1 испытуемом

Вывод: результат неудовлетворительный, надо увеличить тестовую выборку

<h1 align="center"> <img width="374" alt="image" src="https://user-images.githubusercontent.com/25271759/222965273-719625f1-92a4-4775-8740-0c300d5ce1e0.png"> </h1>


Нейросеть обученная на 62 испытуемых, протестированная на 6 испытуемых дает точность распознавания задачи не больше 28%.
По изначальной задумке: в интерфейс в режиме on-line подаются данные, он возвращает результат распознавания задачи.

<h1 align="center"> <img width="374" alt="image" src="https://user-images.githubusercontent.com/25271759/222966055-85821335-5511-4a62-8c54-c10411744e83.jpeg"> </h1>

Нейросеть обученная на 62 двух испытуемых и протестированная на 6 может быть индивидуально «дообучена» на одном конкретном испытуемом

Испытуемый №97 был разбит в соотношении 90% доп. обучающая выборка, 10% на тестовую выборку
"Дообучение" проводилось на 50 эпоха (в принципе достаточно 15-20)

Итог:
- Точность на обучающей выборке 99,90% на одном испытуемом (дообучение)
- Точность на тестовой выборке 80,53% на одном испытуемом (дообучение)

<h1 align="center"> <img width="404" alt="image" src="https://user-images.githubusercontent.com/25271759/222966122-c75f611c-6c31-45a3-8af4-63040541065d.png"> </h1>

<b>Рекурентная нейросеть (вариант 4).</b>
Разработка рекуррентной нейросети для всех испытуемых (62 человек, 6 испытуемый для тестовой выборки) 
на одного испытуемого
- использовались разные батчи 32, 64, 128 в общей сложности на 350 эпохах
- активационные функции tanh на полносвязных слоях
- финальная активационная функция softmax

Итог:

- Точность на обучающей выборке 98,64% на 62 испытуемых
- Точность на валидационной выборке 55,01% на 62 испытуемых
- Точность на тестовой выборке 95,38% на 6 испытуемых

<h1 align="center"> <img width="343" alt="image" src="https://user-images.githubusercontent.com/25271759/223028579-7a7da509-1ddc-4f36-be60-ac26ebdf7bbf.png"> </h1>

Дообучение на испытуемом №97 на последней структуре нейросети. 
Итог:

- Точность на обучающей выборке 99,80% на одном испытуемом (дообучение)
- Точность на тестовой выборке 93,81% на одном испытуемом

<h1 align="center"> <img width="410" alt="image" src="https://user-images.githubusercontent.com/25271759/222966524-115b55d1-3bfe-42d3-b720-f6ceedaa5591.png"> </h1>

Финальная структура модели:

<h1 align="center"> <img width="566" alt="image" src="https://user-images.githubusercontent.com/25271759/222966559-29042436-7c17-44fe-adf0-ca8100dfbf56.png"> </h1>






