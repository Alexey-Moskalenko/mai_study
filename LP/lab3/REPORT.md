# № Отчет по лабораторной работе №3
## по курсу "Логическое программирование"

## Решение задач методом поиска в пространстве состояний

### студентка: Довженко А.

## Результат проверки

| Преподаватель     | Дата         |  Оценка       |
|-------------------|--------------|---------------|
| Сошников Д.В. |              |               |
| Левинская М.А.|              |               |

> *Комментарии проверяющих (обратите внимание, что более подробные комментарии возможны непосредственно в репозитории по тексту программы)*


## Введение

Какие задачи удобным образом решаются методом поиска в пространстве состояний? 
Почему Prolog оказывается удобным языком для решения таких задач?

Пространство состояний представляет собой набор ситуаций. Из каждого состояния возможно перейти в другое состояние путем каких-то действий. Поэтому удобно использовать такой метод, когда у нас есть два заданных состояния -- начальное и конечное, и число всевозможных состояний конечно. Если представить такое пространство как граф, где вершинами являются состояния, то путь от начальной вершины до конечной будет показывать набор состояний, являющийся решением задачи. В итоге, такие задачи сводятся к задаче поиска в графе. Основные стратегии решения такой задачи, которые я использовала в своей работе, -- поиск в глубину, поиск в ширину и поиск с итеративным погружением.

Для представление графа в программировании обычно используют матричное представление, где граф задается своей матрицей смежности. В Прологе граф описывается предикатами путем явного перечисления всех дуг в виде пар вершин. Задание графа при помощи дуг является более гибким, чем матрица смежности, поскольку дуги могут задаваться не только явным перечислением, но и при помощи правил, что позволяет нам описывать очень сложные и большие графы, для которых матричное представление нерационально и вообще не всегда возможно.

## Задание

8. Вдоль доски расположены лунки, в каждой из которых лежит красный, белый или синий шар. Одним ходом можно менять местами два любых шара. Добиться того, чтобы сначала шли все красные шары, все синие -- последними, а белые -- посередине. Решить задачу за наименьшее число ходов.

## Принцип решения

Основной принцип решения задачи состоит в следующем: идем по списку, перебирая по 2 шара из списка, проверяем лежат ли эти шары в правильной последовательности, если нет, то меняем их местами. Я использовала 3 алгоритма поиска: поиск в глубину, поиск в ширину и поиск с итеративным погружением. Они отражены в предикатах search_dpth, search_bdth и search_id соответсвенно.

Рассмотрим часть, общую для всех трех алгоритмов. Предикат prolong нужен, чтобы продлить все пути в графе, предотвращая зацикливания. 
```prolog
prolong([X|T],[Y,X|T]):-
    move(X,Y),
    \+ member(Y,[X|T]).
```

Предикат move отражает переход между состояниями в графе. Во втором правиле проходим по списку, представляющему одно состояние, меняем элементы, стоящие не на своем месте, что позволяет получить другое состояние. Предикат between генерирует все целые числа от 0 до Len, т.е. генерируем все числа от 0 до длины списка, затем проверяем, правильно ли стоят элементы с меньшим порядковым индексом относительно элементов с большим порядковым индексом, и меняем эти элементы.
```prolog
move([H|_],Res):-
    move(H,Res).

move(st(L),st(ResL)):-
    length(L,Len),
    Len1 is Len - 1,
    between(0,Len1,A),
    between(0,Len1,B),

    A @< B,
    check_correct(L,A,B),
    swap_elem(L,A,B,X),

    ResL = X.
```

В предикате check_correct получаем N-ый и M-ый элементы списка. За синим шаром не могут лежать красный или белый, за белым шаром не может лежать красный.
```prolog
check_correct(L,N,M):-
    getNthElem(L,X,N),
    getNthElem(L,Y,M),
    ((X == blue, Y == red);
    (X == blue, Y == white);
    (X == white, Y == red)),
    !.
```

## Результаты

Приведите результаты работы программы: найденные пути, время, затраченное на поиск тем или иным алгоритмом, длину найденного первым пути.

Результат работы:
```prolog
?- search_dpth(st([red,blue,white,red,red,white,white,blue]),st([red,red,red,white,white,white,blue,blue])).
DFS START
st([red,blue,white,red,red,white,white,blue])
st([red,white,blue,red,red,white,white,blue])
st([red,red,blue,white,red,white,white,blue])
st([red,red,white,blue,red,white,white,blue])
st([red,red,red,blue,white,white,white,blue])
st([red,red,red,white,blue,white,white,blue])
st([red,red,red,white,white,blue,white,blue])
st([red,red,red,white,white,white,blue,blue])
DFS END

TIME IS 0.0274660587310791

true .
?- search_bdth(st([red,blue,white,red,red,white,white,blue]),st([red,red,red,white,white,white,blue,blue])).
BFS START
st([red,blue,white,red,red,white,white,blue])
st([red,red,white,blue,red,white,white,blue])
st([red,red,red,blue,white,white,white,blue])
st([red,red,red,white,white,white,blue,blue])
BFS END

TIME IS 0.02316880226135254

true .

?- search_id(st([red,blue,white,red,red,white,white,blue]),st([red,red,red,white,white,white,blue,blue])).
ITER START
st([red,blue,white,red,red,white,white,blue])
st([red,red,white,blue,red,white,white,blue])
st([red,red,red,blue,white,white,white,blue])
st([red,red,red,white,white,white,blue,blue])
ITER END

TIME IS 0.015495061874389648

true .
```

## Выводы

Все три алгоритма справились со своей задачей, но наиболее эффективно, как видно по результатам времени, оказался поиск с итеративным погружением. Он нашел решение быстрее других за наименьшее число шагов. В этом нет ничего удивительного, потому что он является некой модификацией поиска в глубину и в ширину. Отличие его в том, что он ищет кратчайший путь, т.е. если путь нашелся, алгоритм закончил работу и дальше не будет перебирать варианты. Алгоритм итеративного погружения вбирает в себя лучшие черты поиска в глубину и в ширину.

Поиск в ширину имеет экспоненциальную сложность как по времени, так и по памяти (на определённых входных данных он может привести к переполнению стека и падению программы, что и произошло со мной на одном из тестов). Поиск в глубину, весьма экономичен по памяти, но в своей наивной реализации неустойчив, и расточителен по времени, как и поиск в ширину. Конечно, нам может повезти, и решение найдется сразу, но на практике это маловероятно.

Для различных задач подходят различные виды поиска, и выбор должен зависеть от цели. В условиях ограничения по памяти лучше использовать поиск в глубину, а с целью поиска кратчайшего пути -- поиск в ширину. Поиск с итеративным углублением хоть и избегает экспоненциальной сложности, но пригоден только для самых простых задач. Конечно, лучше всего использовать эвристический поиск, что чаще и делают на практике. В отличие от использованных мной алгоритмов, эвристический поиск ищет в пространстве состояний более целенаправленно, т.к. имеет функцию оценки состояния. Вообще стратегии использования пространства состояний очень удобны, мне не нужно было для каждого поиска формулировать задачу заново или вносить в формулировку изменения.