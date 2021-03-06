# Бейсов анализ

Избиране на следващо действие по време на създаване на тестов случай пряко зависи от увереността във взимането на правилното решение. Несигурността от избиране на действие може да бъде моделирана посредством Бейсов подход.

Нека $\theta$ е неизвестна стойност, която може да е скаларна, векторна или матрица. Методите за статистически извод (inference) могат да ни помогнат да я намерим. Класическият статистически подход третира $\theta$ като фиксирана стойност. Единствената информация, която използваме за намиране на неизвестната стойност, идва от данните, с които разполагаме. Изводът се базира на резултат, получен от фунцкията на правдоподобие на $\theta$, която свързва стойности от $p(y|\theta)$ с всяка възможност на $\theta$, където $y = (y_1,...,y_n)$ е вектор с наблюдавани стойности.

Бейсовият подход третира $\theta$ като случайна стойност. За достигане на извод се използва разпределението на параметри при дадени данни $p(\theta|y)$. Това разпределение се нарича апостериорно. Освен функцията на правдоподобие, Бейсовият подход включва априорно разпределение $p(\theta)$, което представя вярванията ни за $\theta$ преди да се разгледат данните.

Теоремата на Бейс дава връзка между фунцкията на правдоподобие и априорното разпределение:

$$p(\theta|y) = \frac{p(\theta|y)p(\theta)}{p(y)}$$

където:

$$p(y) = \int p(y|\theta)p(\theta)d\theta$$

Формулата на Бейс може да бъде пренаписана по следния начин:

(@ref_for_eqn1) $$p(\theta|y) \propto p(\theta|y)p(\theta)$$

тъй като $p(y)$ не зависи от $\theta$

Когато $\theta$ е многомерна величина може да напишем уравнение (@ref_for_eqn1) използвайки маргиналните апостериорни разпределения като например:

$$p(\theta_1|y) = \int p(\theta|y)d\theta_2$$

където $\theta = (\theta_1, \theta_2)$. В много случаи резултатите са многомерни и точни изводи може да бъдат направени само аналитично. Поради тази причина често се използват приближения.

## Монте Карло алгоритми за Марковски Вериги (MCMC)

MCMC алгоритмите правят неявно интегриране като взимат извадки от апостериорното разпределение. По този начин се намират приближения на стойностите, от които се интересуваме.

В съществото си тези методи създават Марковска верига с апостериорното разпределение на параметрите като стационарно разпределение. Когато веригата е крайна и повтаряща се, стойността на $\theta$ може да бъде оценена от извадки на средни пътища. Генерираните извадки $\theta^{(t)}, t=1, \ldots, N$ от това разпределение дават представа за целевото разпределение.

### Метрополис-Хастингс алгоритъм

Този алгоритъм е предложен от Metropolis [@metropolis1953equation] и по-късно генерализиран от Hastings [@hastings1970monte]. Методът създава Марковска верига с желаното стационарно разпределение. Алгоритъмът избира кандидат стойност $\theta'$ от предварително избрано разпределение $q(\theta, \theta')$, където $\theta' \neq \theta$. Избраната стойност $\theta'$ се проверява чрез приеми-откажи метод (accept-reject step), за да се подсигури, че принадлежи на целевото разпределение.

### Извадки на Гибс

Този метод, предложен от Geman и Geman [@geman1984stochastic], често се представя като специален случай на Метрополис-Хастингс алгоритъма.

## Извод със свободни вариационни параметри

Variational Inference (VI) методите обикновено предлагат по-добри резултати спрямо MCMC, когато времето за изпълнение е ограничено. Допълнително предимство на тези подходи е, че те са детерминирани. Систематичната грешка и дисперсията се приближават до 0 при MCMC методите, за колкото повече време бъдат оставени да се изпълняват те. Тези свойства правят MCMC алгоритмите много ефективни на теория. В практиката обаче, времето за изпълнение и изчислителната мощ са ограничени. Това налага търсенето на по-бързо методи дори когато това намаля точността на получените резултати.

Този тип методи дефинират приближено вариационно разпределение $q_\omega(\theta)$, параметризирано от $\omega$, с лесна за оценяване структура. Искаме приближеното разпределение да е максимално близко до това на апостериорното. За целта свеждаме задачата до оптимизационна и минимизираме Kullback-Leibler (KL) [@kullback1951information] отклонението спрямо $\omega$. Интуитивно, KL измерва приликата между две разпределения:

$$KL(q\omega(\theta)\,||\,p(\theta|x, y)) = \int q\omega(\theta)log\frac{q\omega(\theta)}{p(\omega|x, y)}d\omega$$

(Define x,y - dataset)

Този интеграл е дефиниран, когато $q\omega(\theta)$ е непрекъсната спрямо $p(\theta|x, y)$. Нека $q^*_\omega(\theta)$ е минимизираща точка (може да е локален минимум). Тогава KL може да ни даде приближение на апостериорното разпределение:

$$p(y^*|x^*, x, y) \approx \int p(y^*|x^*, \theta)q^*_\omega(\theta)d\theta =: q^*_\omega(y^*|x^*)$$

VI методите заменят изчисляването на интеграли с такова на производни. Това е много подобно на оптимизационните методи използвани в DL. Основната разлика се състой в това, че оптимизацията е върху разпределения, а не точкови оценки. Този подход запазва много от предимствата на Бейсовото моделиране и представя вероятностни модели, които дават оценка на несигурността в изводите си.

## Бейсови Невронни Мрежи

Един от големите недостатъци на съществуващите архитектури на невронни мрежи е, че изводите, които получаваме, са оценки на точки. Моделите не казват до колко са сигурни в предложените резултати. Когато например един лекар получи резултат от даден модел, той трябва да знае защо и как моделът е стигнал до него. Бейсовата статистика може да даде отговор на тези въпроси [@gal2015dropout]. Дори при модели използващи RNN, Бейсова интерпретация на задачата дава по-добри резултати от съществуващи такива [@gal2016theoretically].

Бейсови невронни мрежи, предложени в края на 80-те години [@kononenko1989bayesian] и задълбочено изучавани по-късно [@mackay1992practical; @neal2012bayesian], предлагат вероятностна интерпретация на моделите за дълбоко обучение, като представят теглата им като вероятностни разпределения. Този тип модели са устойчиви на пренастройване (overfitting), предлагат оценки на несигурността и могат да се тренират върху малко на брой данни.

### Оценка на несигурността

