---
title: |
    Ранжирующие бандиты
author: Ершов Василий
bibliography: sources.bib
link-citations: true
lang: russian
mainfont: "Times New Roman"
margins: 1.5cm
---
#  Многорукие бандиты.

![Бандиты](img/bandit.png)

Рассмотрим следующую задачу: игрок приходит в казино, в котором стоит $n$ игровых автоматов (игровые автоматы (one slot machine) называют бандитами, отсюда и название),  у каждого из которых есть ручка, за которую можно «дернуть». Каждый «бандит» при дергании ручки дает какой-то выигрыш. Задача игрока — найти «лучшего» бандита и заработать как можно больше денег. 
Основная проблема игрока — он никогд не знает точно, какая же из ручек «оптимальна». Каждый раз при выборе ручки у него есть только информация о предыдущих испытаниях и две «базовые» стратегии — либо использовать тот автомат, от которого было больше всего пользы до этого — эксплуатирующая стратегия, либо некоторым образом пытаться использовать другие автоматы для того, чтобы понять, а не дадут ли они существенную пользу в будущем. Оптимальная стратегия игры — комбинация exploiation и exploration (exploitation-exploration tradeoff).

Прежде, чем переходить к ранжирующим бандитами, нам требуется сформулировать базовое понимание того, что такое задача о многоруком бандите более формально. 
Фомрализаций существует несколько, но мы, в качестве примера, рассмотрим только бандитов со случайным выигрышем (stochastic multi-armed bandits, SMAB). 

В  данной постановке выигрыш является случайной величиной, зависящей от «ручки». Есть иная постановка— **adversarial bandits**, в которых казино, на основе действий игрока, может менять выигрыши ручек. Первая постановка задачи близка к теории вероятностей и статистике, а вторая к теории игр.  

Рассмотрим формализацию задачи. У нас имеется множество ручек $A=\\{a_i\\}, 1 \leq i \leq k$. Задан дискретный поток времени $1, 2…$, в момент времени $t$ игрок выбирает ручку $a(t)$ и получает некоторый выигрыш $g_t = g_t(a(t)) = g_{a, t}$ (gain, reward, часто обозначают за $r$, но для того, чтобы не «путать» с regret, будем обозначать буквой $g$). Ручки $a(t)$ выбираются игроком на основе некоторой стратегии (policy) — «функции» от истории. 

Цель игрока заключается в максимизации discounted cummulative reward за $T$ шагов:
$$G(T) = E\left( \sum\limits_{t=1}^{T} \lambda^{t}g_{t}\right), 0 < \lambda \leq 1 $$

В обычной постановке задачи $\lambda = 1$. В случае, когда $\lambda < 1$, её можно трактовать как вероятность того, что после момента времени $t$ игра продолжится.

**Внимание: Обычно мы будем предполагать, что ручки стационарны. Т.е. у нас есть $k$ случайных величин, никак не зависящих друг от друга. В каждый момент времени $t$ мы можем наблюдать реализацию одной из этих случайных величин. Задача — найти ту случайную величину, у которой математическое ожидание максимально. В общем случае можно рассматривать случайные процессы в $R^k$, в каждый момент времени которого мы можем наблюдать только одну из координат (а какую — выбор игрока) и задача состоит в нахождении наулучшей координаты.**

Достаточно часто от максимизации кумулятивного выигрыша переходят к минимизации потерь (regret):
$$\mu = \max\limits_{i = 1, … k}Eg(a_i),$$
$$R(T) = T\mu - E\sum\limits_{t = 1}^{T} g_{t},$$
где $g_{a_i}$ — выигрыш $i$-ой ручки, $\mu$ — «лучшая ручка», а параметр $\lambda$ мы считаем равным 1.
Или вводят случайные $r_t = r_t(a_t)$ (regret ручки) и сразу минимизируют 
$$R(T) = E\sum \limits_{i=1}^{T}r_{t}$$ 

Из определения потерь/выиграша видно, что для бейзлайн алгоритма (берущего случайную ручку) потери будет расти линейно (если мы дергаем субоптимальную ручку всегда, то за T шагов в среднем получим разницу в "качестве ручек")

Для такой постановки разработана обширная теоретическая база, доказываются upper/lower bounds на потери/выигрыш, предложены алгоритмы, близкие к или достигающие оптимальную границу (в зависимости от более точной формализации задачи, аналоги неравенств Рао-Крамера, etc).




### Варианты решения SMAB 

Для решения SMAB существует несколько основных алгоритмов. Обычно предполагается, что $0 \leq g(a) \leq 1$ (т.е. выигрыш ограничен).


В случае $\lambda < 1$ и байесовской постановки задачи (предполагаем, что все ручки — из параметрического семейства, для каждой ручки есть априорное распределение) можно показать (Gittins), что существует оптимальная стратегия игры (максимизирующая $G(T)$, при этом не асимптотически). Для каждой ручки надо вычислять так называемый dynamic allocation index (gittins index) и брать ручку с максимальным значением индекса. Сам алгоритм является некоторой вариацией динамического программирования и является очень трудоемким, из-за чего на практике его применять не получается. 
** Важно: из предыдущего абзаца нужно вывести только то, что в практических задачах (когда горизон планирования конечен = время не бесконечно = $\lambda < 1$) существует оптимальный алгоритм, который можно гуглить по gittins index.  **

В связи с тем, что «оптимальный» алгоритм на практике не применим, используют другую технику — Upper confidence bounds (UCB).

Основная идея в том, что для каждой «ручки» $a$ в момент времени  $t$ есть некоторая оценка $\hat{\mu}(a)$ среднего выигрыша этой ручки. Пусть она построена по  $n_a(t)$ наблюдениям. Тогда предлагается построить для каждой ручки доверительный интервал (верхнюю границу) уровня $\delta(t)$:
$$ q(a) = \hat{\mu}(a) + \phi(\alpha, n_a(t), t)$$ 
И выберем ручку с максимальным значением $q(a)$, где $\alpha$ является параметром алгоритма, «регулирующем» exploration/explotation tradeoff.

Типичный пример:
$$q(a) = \hat{\mu}(a) + \sqrt{\frac{\alpha \log t}{n_a(t)}}$$

Cами оценки берутся из теории больших уклонений и других разделов теории вероятностей, на рассмотрение которой у нас нету времени.
** Важно: простоя идея  — используем доверительные интервалы для среднего для выбора ручек, увеличивая уровень значимости границы при росте времени. Точные алгоритмы построения получаем из теоретических выкладок, о которых можно найти полезную информацию по ключевым словам. Главное знать о том, что такое есть**

**Замечание:** мы рассматривали ситуацию, когда выигрыш стационарен. В общем случае каждая "ручка" может быть каким угодно случайным процессом, а также разные ручки могут быть "зависимы". Чем сложнее модель, тем сложнее придумать для нее "хорошее" решение. 

**Краткий смысл данного раздела** 
Постановка задачи о многоруких бандитах, пример формализации задачи. Краткий экскурс в историю вопроса и набор ключевых слов для дальнейшего изучения. 

**Опциональная полезная информация**

1) Видимо лучшей UCB на данный момент является KL-UCB, которая, в частности, для выигрышей, распределенных по бернулли, достигает нижней границы на потери (т.е. асимптотически опимальна). 
Для бернулли оптимально означает, если не вдаваться в подробности, примерно следующее:

 $$\liminf \limits_{T \rightarrow \infty} \frac{R(T)}{\log T} \geq C(\mu_1, …, \mu_k),$$
 где константа C зависит от того, насколько близки математические ожидания друг к другу. Так, если они "почти" не отличаются, то константа большая, а если одна ручка 0.8, а другая 0.2,то константа маленькая.

 ** Последнее выражения можно считать, как один из теор. результатов, но дальше будут и другие. Стоит понимать, что средний регрет растет как минимум логарифмически **


2) Помимо UCB для решения SMAB, в байесовской постановке SMAB, используют Thompson sampling.


**Еще одно важное замечание**

Нам, в дальнейшем, бандиты из данного раздела в основном нужны будут как некоторый "черный ящик", которые будут являться составной частью более сложного алгоритма. Если этот "черный ящик" будем обладать некоторыми хорошими свойствами, то для более сложного алгоритма будет возможно доказать некоторые теоретические результаты. 


# Ранжирующие бандиты


### Постановка задачи

Для начала рассмотрим неформальное описание решаемой задачи. 
**Отмечу, что сразу и пример прикладной задачи, хотя документы и пользователи у нас "абстрактные"**

Есть некоторый поисковик/рекомендательная система/etc. Пользователи задают в эту систему запросы, на запросы надо формулировать выдачу из $k$ документов. Запросы могу повторяться, пользователи могут повторяться, и вообще может происходить что угодно. Пусть пользователь задает запрос $q$. Например, "ягуар". Для каждого запроса $q$ есть множество документов $\mathbb{D} = \mathbb{D}(q)$. Требуется выбрать $k$ документов, показ которых удовлетворит пользователя (т.е. он найдет то, что искал). (документы, которые подходят под запрос, называют релевантными). Делать все это требуется так, чтобы получить максимальный профит. Задача не формально в том плане, что требуется определять, что такое "удовлетворит пользователя" и что такое "максимальный профит". Второе в обычной жизни скорее всего что-то среднее между деньгами и довольными пользователями, а некоторую формализацию мы рассмотрим немного позже.

Таким образом, цель поисковой системы — правильно отвечать на запросы пользователей — чтобы все находили то, что нужно (как мы знаем, google и yandex находить мифопоэзис не умеют…). 

Важно отметить, что конкретного пользователя система не знает, остался ли он доволен, и имеет только неявный feedback о том, на какие документы и «как долго» кликал пользователь. Что такое "неявный feedback" также требует формализации.

В итоге: мы хотим разработать систему, которая будет показывать «оптимальные» документы для пользователя. Различные формализация понятия «оптимально» приведут нас к различным алгоритмам.

**Важно: в качестве неявного фидбэка мы будем рассматривать клики по документам. Это очень филосовский вопрос, насколько это полезная информация, но мы об этом думать особо не будем. Поэтому нашей целью будут алгоритмы, которые приводят к тому, что абстрактный пользователь кликает как можно чаще. **


Как видно, все очень похоже на то, что мы видели до этого:

1. Есть некоторый feedback о том, удачный ли мы результат показали пользователю
2. Можем жадно показывать документы с максимальным числом кликов на текущий момент
3. Можем пытаться искать новые документы, про кликабельность которых мы ничего не знаем

Эта задача практически в неформальной постановки очень похожа на бандитов и отсюда идея – попробовать решить ее похожим  на бандитов способом. 

Далее мы рассмотрим несколько подходов (а точнее 2). Сначала мы рассмотрим способ сведения задачи к тому, что мы обсуждали в разделе про бандитов.




### Сведения задачи к MAB/SMAB

Основные особенности первого семейства подходов, которые мы будем рассматривать:

1. Рассматриваем алгоритмы в рамках одного запроса. Для разных запросов — отдельные «экземпляры» алгоритма. **Таким образом, у нас всегда один запрос**.
2. В качестве ручки — список из $k$ документов, который показываем пользователю.
3. Сводим задачу к обычным MAB

Если смотреть не формально, то в качестве "казино" выступают пользователи. 
В качестве ручки — фиксированный набор из $k$ документов, который нравится максимальному числу пользователей.

**Мы пока еще ничего не формализовали. Конкретные формализации и постановки задач будут описаны при рассмотрении алгоритмов**

Первый алгоритм, который мы рассмотрим — ranking bandtis by Radlinski. 

Решается следующая задача: 

1) Дано множество документов $\mathbb{D}$ мощности $n$
2) Есть некоторая «популяция» пользователей $U$. 
3) От ранжирующей системы требуется выдавать $k$ таких документов, что пользователь кликнет хотя бы на один из них (Цель — минимизация adandonment rate, т.е. кол-ва пользователей, которые ушли ни с чем).  

Это задача все еще слишком общая и сложная. Нам требуется ввести некоторые предположения о том, как ведет себя пользователь на выдаче и что такое вообще "пользователь".

Для начала рассмотрим простую модель поведения пользователя на выдаче: 

1. Пользователь открывает страницу с выдачей
2. Смотрим на документы (точнее на snippets) по-порядку, начиная с первого.
3. Как только находим интересующий документ — кликаем и «уходим».

Это одна из наиболее простых моделей просмотра выдачи, с которой уже возникает много сложностей. Только ее мы и будем рассматривать. 

Сама система будет показывать документы в моменты времени $1…T…$. В момент времени $t$ к нам будет приходить пользователь $u_t$.

Формально пользователь для нас будет набором набором вероятностей $\left\lbrace p_{t}(d)\right\rbrace$, где $p_{t}(d)$ — вероятность пользователя кликнуть на документ $d$, если он до него дошел. 

Чуть позже нам потребуется еще «идеальный» кейс — ситуация, когда есть множество релевантных для пользователя $u_t$ документов $A = \left\lbrace d_1, …, d_m\right\rbrace$, и если пользователь его видит, то кликает с вероятностью 1 (т.е. более простая ситуация).

Тепер определим, что мы понимаем под выигрышем: 

$$g_t(u_t) = \begin{cases}
    1, & \text{пользователь кликнул на какой-нибудь документ}.  \\
    0, & \text{иначе}.
  \end{cases}
$$

**Важно: в текущей постановке мы не будем накладывать никаких ограничений на пользователей. К нам смогут приходить пользователи (которых направил конкуретн ;)), цель которых — сделать нам хуже некуда (т.е. пытающиеся сломать наш обучающийся алгоритм). **

Пусть $a_t$ — список показанных в момент времени $t$ документов.

Будем максимизировать 
$$G(T) = E\sum\limits_{t=1}^{T} g_t(a_t, u_t)$$

Таким образом, наша задача, как уже упоминалось — собрать как можно больше кликов ни смотря ни на что.

**Важно:** математическое ожидание берется относительно всего случайного, что только может быть - как действий среды (пользователей), так и самого алгоритма, дергающего ручки 


Идея алгоритма, который решает такую задачу очень проста. 

Для каждой позиции $1…k$ в запросе вводится экземпляр бандитского алгоритма $MAB_{i}$. $MAB$ мы рассматриваем как некоторый черный ящик, который умеет выбирать одну из $n$ ручек, где каждой ручке сопоставлен уникальный документ, получать выигрыш $0$ или $1$ и искать "оптимальную" ручку. Напомню, что хотя мы касались в основном SMAB, существуют другие алгоритмы, которые, в том числе, умеют "бороться с нестационарностью".

Далее для каждого пользователя будем действовать по следующей схеме:

Пусть $a_t[i], i = 1…k$ — показанный для $i$-ой позиции документ  в момент времени $t$

Для каждой позиции $i=1…k$ 

1. Выбераем с помощью $MAB_i$ документ.
2. Если документ встречается на $a_t[1], …, a_t[i-1]$, то заменяем его на случайный еще не выбранный документ
3. Показываем выбранный документ на позиции i (записываем в a_t[i])

После того, как по алгоритму выше сгенерирован  выдачу $a$, показываем $a$ пользователю.

Мы рассмотрели алгоритм для "дерганья ручки" — генерации некоторой выдачи.
Теперь соберем feedback. Пусть пользователь кликает на какой-то документ, не умаляя общности будем считать, что это последний.

Тогда если документ на $k$ позиции не случайный, то $MAB_{k}$ получает выигрыш 1, иначе 0. Все остальные документы получают выигрыш 0.
**Интуитивное понимание того, что происходит: каждый бандит штрафуется за то, что показывает документы, на которые не кликают. При этом за счет того, что мы предполагаем, что пользователь смотрим по порядку, у нас разные бандиты должны будут начать показывать разные документы**

**Простой пример для понимания: представьте, что надо показывать один документы. Тогда задача сводится к MAB, который выбирает документ, по которому чаще всех кликают**


Алгоритм очень прост в реализации, не требует никаких сложных вычислительных трюков и прочего. Важно писать код так, чтобы сам алгоритм не был завязан на какого-то конкретного бандитского алгоритма (типа UCB).

Мотивацией к применению данного алгоритма служит следующий **теоретический результат**

При некоторых предположения на алгоритм $MAB_i$, которые мы опустим, можно получть следующую качество алгоритма за $T$ шагов:
$$G(T) = (1 - \frac{1}{e})OPT - O(k\sqrt{T\log n})$$

Давайте поймем, что здесь написано. Во-первых, OPT является оптимальным выигрышем (количеством кликов) для некоторой специфичной задачи, а именно более простой версии того, что мы рассматриваем:

1. У нас идеальная ситуацию, когда пользователи знаю, чего хотят, и вероятности $p_{ti}$ равны 0 или 1. (т.е. клики детерменированы)
2. Множества релевантных документов для пользователей известны. Т.е. мы знаем все документы, на которые конкретный пользователь будет кликать, а на какие не будет 
3. Решаем задачу в оффлайн. Мы знаем, что к нам придут пользователи $u_1, …, u_{T}$. Мы ищем такой набор документов, что $G(T)$ будет максимально (замечание: набор документов один на всех пользователей, а не лучший для каждого разумеется.)

Таким образом  для каждого документа есть набор пользователей, которые на него кликают. Итого есть $S_1, …, S_n$ множеств. Требуется выбрать из этих множеств $k$ так, чтобы $\cup S_i$ было максимальным (накрыть максимум пользователей)
Эта задачи $NP$-трудная.  OPT — оптимальное решение.

**Еще раз: задача найти такие $k$ документов, что мы соберем максимальное количество кликов вычислительно очень сложная**
**Обратите внимание (и осознайте): Если смотреть на пользователей и алгоритм как на случайный процесс, то этот результат дает оптимальное решение на любой "траектории" этого случайного процесс. Оптимально решение значит такой фиксированный набор документов, что на этой траектории по нему кликнуло максимум пользователей)

**(1 - 1/e)OPT — жадное решение** задачи выше,  лучше которого сделать «нельзя» (или докажем какой-нибудь факт про  NP)

Таким образом, рассмотренный алгоритм работает на примерно $k\sqrt{T\log n}$ хуже, чем алгоритм, который "знает все".

Далее, обратим внимание, что OPT зависит от $T$. Если, как обычно, посмотрим на средний выигрыш $\frac{OPT}{T}$, то увидим, что «средний» выигрыш стремится к оптимальному среднему выигрышу, при этом скорость $\sqrt{T}$ (по модолю жадной аппроксимации, лучше которой "нельзя").


**Вывод: в такой постановке алгоритм делате что-то полезное** 

**Если что, это вполне себе математическое обоснование метода.**

Еще замечание: 

1. «Бесплатно» получаем оптимальных бандитов и на префиксах из первых $l$ документов
2. Для доказательства оценки требуется выполнение некоторых условий на выигрыш для базового MAB-алгоритма. При этом для доказательства используется adversarial постановка задачи, про которую мы практически ничего не упоминали
3. В таком случае граница качества — worst case, т.е. что бы не происходило, будем не слишком плохие результаты показывать. В частности, выигрыш может быть нестационарным.
4. В статье использовали Exp3 алгоритм для базовых бандитов (он adversarial).
5.  Если интересы пользователей не меняются существенно со временем, то можно и нужно использовать алгоритмы на основе UCB, т.к. они, скорее всего, дадут «лучший» результат.
6.  **Важно: алгоритм нацелен на худшую ситуацию. Как известно, методы, хорошо работающие в худшем случае, плохо работают в "среднем", поэтому далее рассмотрим алгоритм для "среднего" случай (формально смотри далее)**


Одна из практических задач, которая решается рассмотренным  алгоритмом — разнообразие выдачи. Существуют запросы, которые для разных пользователей означают разное. Например, на запрос «ягуар» один пользователь может иметь в виду животное, а другой автомобиль. Соответсвенно на выдачи надо показывать, с одной стороны, как можно более разнообразный набор документов, а с другой такой, что по нему будут «хорошо» кликать.
 

### Cascading bandits

В предыдущем разделе мы рассматривали алгоритм, в котором "среда может сопротивляться" — т.е. нам могут специально присылать "плохих" пользователей. 
Если отказаться от такого предположения и ввести больше ограничений в модель из прошлой задачи, то можно предложить рассмотренный далее алгоритм.

Предполагаем, что  модель поведения пользователя на выдаче из $k$ документов следующая: 

1. Как и раньше, смотрим последовательно. 
2. Вероятности кликов по документам не зависят от пользователя. Т.е. для каждого документа задана вероятноть $p(d)$ и она стационарна. Кроме того, она не зависит от предыдущих просмотренных документов. (Заметим, что в предыдущем случае мы такого также не предполагали).
3. Как и раньше, кликает пользователь только на 1 документ.

Из формулировки модели следует, что

1. Вероятность посмотреть на $d_i$ равна 
 $$\prod\limits_{j=1}^{i-1} \left(1 - p(d_j)\right)$$
2. Вероятность кликнуть хотя бы на 1 документ: $$1 - \prod\limits_{j=1}^{k} \left(1 - p(d_j)\right)$$
 
 Цель — выдать такой список документов, что вероятность того, что пользователь кликнет на какой-либо документ максимальна. 

За счет независимости выигрыш зависит только от $p(d_i)$ — «кликабельности» документа:
$$ \mu_i = p(a_t[i])$$
$$c_i = Ber(\mu_i)$$
$$g(a_t, u_t) = 1 - \prod\limits_{i=1}^{k}  (1 - c_i)$$
$$Eg(a_t, u_t) = 1 - \prod\limits_{i=1}^{k}(1 - \mu_i)$$

Как обычно определим потери как разницу между лучшим набором документов и тем, что показал пользователь.
$$R(T) = E\left(\sum\limits_{t=1}^{T} \left(g^{\*}(u_t) - g(a_t, u_t)\right)\right)$$
где $g^{\*}$ — алгоритм, показывающий всегда оптимальный набор документов (можно показать, что этот набор — документы с наибольшей вероятностью клика. Для тех, кому не очевидно — упражнение по теор. веру =)).

Алгоритм для такой постановки задачи предлагается следующий:

1. Выберем какой-нибудь UCB-like алгоритм. Рекомендуют KL-UCB, как наиболее точную
2. Для каждого документа будем хранить кол-во раз, которое на него кликнули и кол-во раз, которое на него посмотрели
3. UCB будем вычислять для CTR (click-through rate) — сколько раз кликнули при скольки при скольки просмотрах (т.е. просто для среднего).
4. На каждом шаге алгоритма — выбираем для показа $k$ документов с максимальными UCB (т.е. такие, которые могут в будущем дать много профита.)
5. Для обновления UCB берем все документы до позиции последнего клика. Если пользователь не кликнул ни на один документ, то берем все документы. После этого все документы до позиции последнего клика считаем как просмотренные. На тот, который кликнул считаем и как кликнутый. Теперь с помощью UCB мы можем обновить статистики для этих документов (т.е. считаем, что «ручки», соответсвующие этим документам были сыграны)
 

Про данную задачу и алгоритм известны следующие теор результаты:

1. Для любого алгоритма, решающего задачу $\liminf \frac{R(T)}{\log T} \geq C(k, n, p)$ (в том же смысле, что и в рао-крамере — равномерная граница снизу на любую возможную задачу).
2. Для KL-UCB и UCB1 $R(T) = O(n\log T)$, константы уменьшаются при увеличении $k$. Здесь $n$ в $O$ только для того, чтобы показать, что верхняя граница линейна по кол-во документов, из которых выбирают.

Стоит отметить, что авторы статьи получают результат, не зависящий от порядка, в котором показываются документы. Есть две стратегии того, как можно показывать топ $k$ документов по UCB:

1. По возрастанию UCB (первый — с самой низкой UCB)
2. По убыванию

С одной стороны, первый вариант «интуитивно» должен давать в будущем более точные оценки (позволяет «быстрее» узнать «полезную инфомрацию» про документы), с другой вариант 1 может раздражать пользователей. На симулированных экспериментах вариант 1 был лучше, но почему авторы объяснить не смогли.

**Опциональное замечание:** есть обобщение данной модели на DCM модель, в которой пользователь может кликать на несколько документов:

![DCM model](img/dcm.png)


## Некоторые минусы рассмотренных подходов

В них никак не используется offline-алгоритмы, умеющие «хорошо» ранжировать документы (по сути некоторый prior на то, что есть хорошо и что есть плохо.). Кроме того, эти алгоритмы никак не умеют работать с контекстом — признаками, описывающими документы (и запросы). 

Кратко про то, как обучается формула для ранжирования. Для этого используется supervised learning подход с релевантностью, размеченной людьми и некоторым кол-вом признаков (фактор, features), на основе которых требуется предсказать релевантность документа запросу. 

Классическая схема обучения с учителем:

1. Собираем набор данных  (запрос, документ, релеватность). Для каждого запроса есть несколько документов.
2. Придумываем набор признаков, которые можем посчитать.
3. Обучаем GBDT под оптимизацию ранжирующей метрики (а точнее ее «гладкой» аппроксимации, но для нас это не суть важно).
4. Profit, afaik, state of the art качество.


Один из способов объеденения offline и online алгоритмом выглядит следующим образом:
1. С помощью supervised learning фильтруем документы — оставляем только небольшое количество "хороших" кандидатов
2. С помощью бандитов учимся ранжировать только этих кандидатов.

Но такой подход не оптимальен, ведь к бандитам, обрабатывающего запрос, будет приходит не множество документов, а множество пар (doc, rel), где rel — некоторое число, отсортировав по которому есть шансы получить «хорошее» значение ранжирующей метрики, так что такое подход только частично решает проблему.

Учет признаков задача тоже не самая проста. Один из подходов, который можно применить рассмотрен в следующем разделе. Краткая идея следующая: 
Допустим между релевантностью документа и признаками есть линейная зависимость (линейная по параметрам модели, как обычно). Тогда можно пытаться подбирать эту линейную зависимость в online на основе информации о кликах пользователей. А начальное значения для параметров модели можно подобрать и в offline.
Подробнее далее.

## Опцианально: иной подход к задаче. Dueling bandits

Алгоритма основан на иной идее. Будем считать, что у нас есть некоторое нормированное векторное пространство $\mathbb{W}$, задающее ранжирующие алгоритмы. Т.е. $w \in \mathbb{W}$ является функцией от запроса и документа $w = w(d, q)$, выдающая значение «релевантности» документа запросу, по которой затем сортируются документы и берутся первые $k$.

 Будем считать, что умеем сравнивать 2 алгоритма: 
$$P(w_1 > w_2) = \frac{1}{2} + \varepsilon(w_1, w_2)$$

А также показывать пользователю одновременно выдачу, сформированную с помощью $w_1$ и $w_2$. 

Определим потери алгоритма за время $T$ как
$$ R(T) = \sum\limits_{t=1}^{T} \varepsilon(w^{*}, w_1) + \varepsilon(w^{*}, w_2), $$
где $w^{*}$ — лучший алгоритм ранжирования(которого мы не знаем)

Тогда для оптимизации таких потерь предлагается использовать следующий алгоритм:

![Dueling bandint gradient descent](img/dbgd.png)

При некоторых дополнительных предположения можно показать, что приведенный выше алгоритм действительно будет оптимизировать введенную нами функцию потерь:

1. Существует  диффиренцируемая строго выпуклая utility-functon на $\mathbb{W}$ :
 $$v : \mathbb{W} \rightarrow \mathbb{R}$$
2. Существует $\sigma$ такая, что 
$$P(w_1 > w_2) = \sigma(v(w_1) - v(w_2))$$
$$ \varepsilon(w_1, w_2) = \sigma(v(w_1) - v(w_2)) - \frac{1}{2}$$
Здесь $\sigma$ — link function. Типичный пример link function:
$$ \sigma(x) = \frac{1}{1 + e^{-x}}$$
3. $\sigma, v, \sigma'$  — липшицевы.
4. $\mathbb{W}$ содержим 0,  компактное, выпуклое, ограничено

Тогда доказывается следующий результат про $\Delta_{T} = R(T)$:

![Dueling bandint gradient descent](img/dbgd_regret.png)

Здесь $R$ — радиус шара, в котором содержится $\mathbb{W}$, $L$ — константа липшица для $\varepsilon$ (равна $L_{v}L_{\sigma}$), $d$ — размерность $\mathbb{W}$


Замечание: в постановке считаем, что можем сравнить одновременно два алгоритма. Один из способов это сделать — TDI:

1. Смешиваем выдачи от двух разных ранкеров. 
2. Первый ранкер выбираем случайно
3. Каждый «ранкер» поочередно выбирает лучший документ из тех, что еще доступны
4. Добавляем документ в выдачу (на последнюю позицию), а выбранный документ записываем в «команду» выбравшего его «ранкера»
5. Ранкер, по команде которого больше раз кликнули побеждает.
 

### Offtop про идеи объединения подходов
«Идеи» подходов к объединению двух типов ранжирования:

1. С помощью supervised learning выдаем распределения релевантностей документов по факторам. Используем это распределение внутри бандитских алгоритмов
2. Формулу для ранжирования можно использовать в dueling bandits для начального подбора модели
3. Для некоторых простых классификаторов есть алгоритмы, позволяющие обучаться в online, а также confidence bounds на ошибку классификатора, которые также можно пытаться использовать
  
  



