# Локальная оптимизация

Локальная оптимизация или метаэвристики — это альтернативный глобальному поиску подход к решению оптимизационных задач, для которых не представляется возможным получить точное решение (к примеру, NP-полные задачи).

![](https://upload.wikimedia.org/wikipedia/commons/c/c3/Metaheuristics_classification.svg)

Эти алгоритмы имеют частично недетерминированную природу, т.е. зависят от случайного выбора каких-то начальных параметров (пример: локальный поиск) и/или вероятностной природы принятия решений в процессе работы алгоритма (пример: симуляция выплавки).


## Локальный поиск

В простейшем варианте подход локального поиска состоит в том, чтобы взять какое-то решение задачи (например, случайное) и пытаться улучшить его с помощью простых преобразований до тех пор, пока это возможно. Например, для задачи TSP простейшим методом улучшения является метод 2-opt, который удаляет 2 произвольных ребра из маршрута и заменяет их на другой вариант ребер, соединяющих вершины из этих ребер. Есть только один вариант такой замены. Например, если исходный маршрут был такой: `1 - 2 - 3 - 4 - 5`,— то замена ребер `1 - 2` и `4 - 5` приведет к новому маршруту: `1 - 4 - 3 - 2 - 5`.

Проблема локального поиска — это попадание в локальный оптимум.

## Симуляция выплавки

Один из вариант решения проблемы локальных оптимумов является подход Simulated annealing (Симуляция выплавки, иммитация отжига). Согласно ему текущий маршрут может меняться не обязательно на более оптимальный, но и (иногда) на менее оптимальный (с определенной вероятностью). Общая схема следующая:

1. Получить исходное решение
2. Рассмотреть случайную вариацию этого решения
3. Если вариация лучше, принять ее, если нет, то отбросить ее с вероятностью, которая зависит от разницы между оценкой текущего варианта и предыдущего, а также от итерации (чем больше номер итерации, тем больше вероятность отказаться).
4. Запоминание исторически самого лучшего результата, чтобы вернуться к нему в случае, если алгоритм пошел неверным путем (слишком долго не получается улучшить оптимальный результат).

У алгоритма много параметров, которые необходимо подстраивать для конкретной задачи:
- "график" (функцию) изменения вероятности
- метод семплирования (выбора) кандидата
- количество итераций до остановки или рестарта

## Семплирование

Семплирование — это создание случайной выборки из какого-то набора элементов, распределение которых известно. Для семплирования `n` элементов небходимо `n` раз применить метод семплирования одного элемента из множества. Такое семплирование может быть с или без извлечения элемента из исходного множества.

Простейшие подходы к семплированию:

1. Семплирование из набора `n` равномерно распределенных элементов — выбор элемента с индексом `random(n)`
2. Семплирование из набора `n` элементов, которые имеют разный вес (вероятность выбора). Такой набор можно преставить как список пар элемент-вес. Семплирование может быть выполнено проецированием всего набора в отрезок [0,1] так, что каждому элементу будет отдан подотрезок, размер которого пропорционален его вкладу в общий вес.

    Для набора из `n=4` элементов `a:2, b:1, c:5, d:2` отрезок будет иметь такой вид:

       0 .. a .. 0.2 .. b .. 0.3 .. c .. 0.8 .. d .. 1

    Далее для выбора случайного элментра генерируется случайное число в диапазоне от 0 до 1 и выбирается тот элемент, в отрезок которого попало это число.
3. Для семплирования из бесконечного набора элементов, который обрабатываетс послеовательно (как поток/stream), используется метод резервуарного емплирования (reservoir sampling). Он работает так же, как и предыдущие 2, только итеративно. При последовательной обработке бесконечного набора на первом шаге всегда выбирается первый и единственный элемент, на втором шаге с вероятностью 1/2 (для равномерного распределения) либо сохраняется выбранный элемент, либо он заменяется на второй. На n-ом шаге с вероятностью `1/n` выбирается новый элемент, а с вероятностью `(n-1)/n` сохраняется текущий.

## Эволюционные алгоритмы

Эволюционные алгоритмы — это группа различных методов решения задач оптимизации (и не только), которые воспроизводят общую схему биологических процессов.

В таких методах оптимизация происходит с помощью создания случайной "популяции" решений и постепенной эволюции их в сторону оптимизации целевой функции с использованием псевдоприродных механизмов (генетические алгоритмы, симуляция муравьиной колонии, пчелиного улья, ...).

Особенности таких методов:

- оптимизация с позиции черного ящика: эти методы никак не учитывают особенности целевой функции, и поэтому могут оптимизировать функции любого вида
- удобная параллелизация
- фактически, каждый метод является фреймворком, который требует определения своих ключевых функций индивидуально для каждой задачи


## Генетические алгоритмы

ГА — это подход к решению задач оптимизации методом постепенного эволюционирования набора решений. Каждое решение в популяции описывается "хромосомой", которые сперва выбираются случайно, а затем оцениваются с помощью фитнесс-функции. После оценки самые лучшие ("элита") могут сохранятся, а остальные подвергаются скрещиванию между собой с последующей мутацией.

### Кодирование хромосомы

Хромосома — это вектор, который может содержать битовые, целочисленные, действительные или другие значения. Кодирование должно быть адекватно задаче. Например, битовое кодирование может быть использовано для задачи рюкзака: для каждой вещи 1 означает, что она включена в рюкзак, а 0 - что нет. Целые числа могут использоваться в задаче TSP: в этом случае хромосома — это последовательность вершин в маршруте, а пространство решения задачи — все перестановки из этих последовательностей.

### Фитнесс-функция

Фитнесс-функция — это функция, которая оценивает качество каждого решения. Как правило, это целевая функция оптимизации.

### Преобразования

- Скрещивание — преобразование пары хромосом в другую пару, которая является комбинацией частей каждой из исходных хромосом.
- Мутация — случайное изменение небольшой части хромосомы. Без мутации невозможно получить новые решения и выйти из ситуации локального минимума


## Литература

- https://www.youtube.com/watch?v=SC5CX8drAtU
- https://www.reddit.com/r/dataisbeautiful/comments/2go0o6/the_shortest_path_through_the_48_continental 
- https://cs.adelaide.edu.au/~markus/pub/2012lion.pdf
- https://habrahabr.ru/post/209610/
- http://www.theprojectspot.com/tutorial-post/simulated-annealing-algorithm-for-beginners/6
- http://watchmaker.uncommons.org/manual/ch01s02.html
- http://www.obitko.com/tutorials/genetic-algorithms
- http://www.theprojectspot.com/tutorial-post/applying-a-genetic-algorithm-to-the-travelling-salesman-problem/5
- http://stackoverflow.com/questions/4092774/genetic-algorithm-versus-simulated-annealing-performance-comparison-and-use-cas
- https://habrahabr.ru/post/276801/
- https://www.youtube.com/watch?v=H0G1yslM5rc
- https://www.youtube.com/watch?v=aUkBa1zMKv4
- http://blog.cloudera.com/blog/2013/04/hadoop-stratified-randosampling-algorithm/
- https://www.quora.com/What-is-an-intuitive-explanation-of-reservoir-sampling

