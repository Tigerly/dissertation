# Изучаване на ГПИ среди

В тази глава изграждаме подход, който ни позволява да изучаваме среда, чието състояние се базира на изображения. По зададено изображение и множество от действия трябва да изберем действие, което максимизира изучената част от средата. По-конкретно, текущото състояние се определя от изображението на приложението и възможните действия с графичните елементи, а изучената част е моментното покритие на код.

Подходът, който използваме се базира изцяло на данните, които средата предоставя. В частност, разработваме модели, които представляват дълбока невронна мрежа, която приема изображения като входни данни и избира действие. Така създадения модел се тренира върху мобилни приложения за Android. Експерименталните резултати показват...

## Related Work

Извличане на характеристики (features) от сензорна информация намалява нуждата от ръчното им закодиране и увеличава скоростта на изграждане на модели. Скорошните открития в областта на дълбокото самообучение доведоха до големи открития в компютърното зрение [@krizhevsky2012imagenet; @mnih2013machine; @sermanet2013pedestrian] и гласовото разпознаване [@dahl2012context; @graves2013speech]. Те използват различни архитектури за дълбоки невронни мрежи, включително конволуции, многослойни персептрони (multilayer perceptrons) и рекурентни невронни мрежи. Използвани са в обучение с учител, без учител. Дълбока конволюционна невронна мрежа беше използвана за създаване на агент, който играе Atari 2600 компютърни игри [@mnih2013playing]. За входни данни е използван видео вход с размери 84x84 пиксела и възможните действия. Работата използва повторение на преживяното (experience replay) [@lin1992self] и надгражда върху Neural Fitted Q-Learning (NFQ) [@riedmiller2005neural]. Още по-добри резултати бяха постигнати като се използва Монте Карло дървета за планиране, които бавно достигат извод, за обучение на дълбоки невронни мрежи, които са многократно по-бързи [@guo2014deep].

## Дадено на агента

Предполагаме, че на стъпка $t$ получаваме изображение и множество от възможни действия, определящи текущото състояние на средата. Искаме да създадем модел, който при подадени така описаните входни данни ни дава апостериорно разпределение, описващо вероятностите всяко от възможните действия да ни доведе до състояние с оптимално увеличение на покритието на програмен код.

Предизвикателството в така поставената задача се състой във факта, че изображенията са сложни многомерни обекти, а оптималното действие във всяко състояние може да е различно от това в което и да е друго. Нашият модел трябва да изгради вътрешно представяне на средата (напр. да научи какво е бутон, граници на отделните елементи и кои действия да използва върху тях) и да научи оптималните действия за всяко състояние.



1. Изображение на ГПИ средата - пример ГПИ. примери + изображения
2. Изучи ГПИ средата - да достигне всички възможни състояние на средата
3. Възможни действия - 

Агентът щракване върху екрана (пример визуален)
Агентът натиска и задържа (пример визуален)
Агентът натиска и влачи курсора (пример визуален)
Агентът scroll-ва нагоре и надолу

![](./assets/mobile_app_structure.png)

## Задачи

- Да постигне 100% покритие на програмния код на даден софтуерен продукт (СП)
- Да генерира поредица от действия с които преминава през всички клонове на дървото, описващо възможните състояния на СП

## Модел на агента

Нека всяко състояние $s$ е представено като изображение и имаме множество от действия $A$, които отговарят на различни графични елементи на екрана. Искаме да изберем действие $a$, което максимизира покритието на код. Предизвикателството се състой в намирането на възможно най-кратките поредици от действия, когато $A$ може да е различно за всяко състояние $s$.

Подобно на [@mnih2013playing] създаваме дълбока конволюционна невронна мрежа, която използва изображения за входни данни.Директна работа с реалните размери на изображение взето от устройството може да изисква прекалено много изчисления (computationally demanding). Съвременните мобилни устройства достигат до 3840x2160 разделителна способност (Syny Xperia Z5 Premium). Въпреки това, физическият размер на екраните на смартфоните достигат до 5.5". Това ги прави далеч по-малки от екраните на настолните и преносимите компютри. Основното взаимодействие с мобилните устройства се извършва чрез различни жестове (натискане, задържане, приплъзване и т.н.) с екрана. Предвид физическите размери на пръстите на човек, екраните не могат да съдържат голямо множество от елементи с които може да се взаимодейства.

### Пространство на състоянието (State space)

Дадено състояние $s$ на средата съдържа цветно изображение $I$ и информация за сегментацията DOM модел $D$, т.е. $s = (I, D)$. Изображението има размер $W \times H \times 3$, където $W$ е широчината на изображението в пиксели, $H$ височината на изображението в пиксели и 3 - броя на цветовете в палитрата (червено, зелено и синьо (rgb)). DOM моделът е представен като списък от текстови елементи, а информацията за сегментацията на изображението е дадена от наредената четворка $(x, y, w, h)$ $x$ абцисната координата, $y$ ординатната ос, $w$ - широчината на сегмента, $h$ - височина на сегмента.

### Пространство на действията (Action space)

Позицията на курсора $m = (m_x, m_y) \in [0, W) \times [0, H)$ се моделира чрез мултиномиално разпределение върху възможните позиции. ГПИ средата не изисква наличие на клавиатура, защото . Възможните действия са: click, drag, scroll-up, scroll-down.

### Представяне на изображенията

Векторното представянето на изображения се базира на напредъка постигнат в компютърното зрение, където Конволюционните Невронни Мрежи (КНМ) са показали, че могат да превръщат сурови изображения в мощни представяния [cite], които позволяват създаването на модели, представящи се на човешко ниво в състезания като ImageNet classification challenge [cite]. КНМ може да се разглежда като функция  $CNN_{\theta_c}(I)$, която извлича характеристики за изображение $I$ и има параметри $\theta_c$

### Модел за избор на действия

Агентът избира действие $a$ във време $t$, когато се намира в състояние $s$. Решението на агента се взима благодарение на Дълбока Бейсова Невронна Мрежа (Deep Baysian Neural Network)

### Определяне на награди

Наградата $r_t$ за всяка стъпка $t$ се дефинира като:

$$r = -1 + C_a$$

където $C_a$ е новият процент покритие на код след избора на действие $a$. "Наказанието", количествено оценено с $-1$ за всяко следващо взето действие "мотивира" агента да се стреми изучи средата максимално бързо. Това спомага за намаляване на възможността за разглеждане на две съседни състояния в цикъл.

### Архитектура на модела

* Входен слой - броят на невроните е равен на броят на пикселите в изображението, което представя средата в текущото време (обикновено 6400)
* 1-ви скрит слой - 2-мерен конволюционен слой с 3 входни канала и 16 изходни. Размерността на ядрото (kernel) е 5.
* 2-ри скрит слой - нормализиращ слой за 16-те изходни канала от предходния слой.
... Повтаряме предходните 2 слоя още 2 пъти
- 7-ми скрит слой - напълно свързан (fully-connected) слой, който сплесква (flatten?) броя на измеренията до 1.
- 8-ми скрит слой - отпадащ (dropout) слой, който изключва *20%* от невроните в мрежата на всяка стъпка.
- Изходен слой - редуцира броя на невроните от предходния слой до броя на възможните действия на Агента

### Цел/Оптимизация/Тренировка/Обучение

Целта на създадения модел е да научи апостериорното разпределение на действията в дадено състояние на средата $s$ - формула. 

Определение 1 (функция на загубата на Хубер). Казваме, че 

Обучението ще се състой в минимизиране на т.нар функция на загубата на Хубер. Тя има свойствата на средна квадратична грешка, когато грешката е малка и тези на средна абсолютна грешка, когато грешката е голяма - това прави модела ни издръжлив (robust) на екстремни стойности (outliers) [cite].

### Памет

Ще запазваме преходите от състояние $s$ до $s'$ при избрано действие $a$ и получената награда $r$, които ще използваме за допълнително обучение на модела. Избирането на случайно подмножество от така запазените преходи

### Оценка на модела за избор на действия

Адекватността на агента, т.е. адекватността на всички избрани от агента действия се измерва чрез т.нар мярка за "съжаление" (regret) - разликата между оптималната обща награда и получената обща награда. Оптималната награда може да се постигне, когато на всяка стъпка $t$, агентът избира оптимално действие $a^*$.

### Намиране на апостериорно вероятностно разпределение на действията

Политиката, която използват агентите в [cite] е епсилон-лакома с намаляща стойност. Тя се отличава с това, че избира случайни действия, за да събере данни в началната фаза на обучението по-късно намаля вероятността за избиране на случайно действие. Обученият агент избира действие с максимална награда. Вместо това, може да опитаме да минимизираме несигурността (uncertainty) на нашата НМ. Това се оказва сравнително лесно ползвайки Thompson Sampling.

#### Thompson Sampling

Thompson sampling е политика, която насърчава агента да изследва средата в която действа, като избира действие с максимална награда, използвайки текущото си познание за средата. В нашият случай, това може да направим като симулираме стохастичен преход през НМ и изберем действието с най-висока очаквана награда.

## Експерименти