
Я собрал чуть больше 120,000 комментириев, из которых нафильтровал 7550 написанных на украинском языке и с высталенной оценкой.
Наивный байесовский классификатор и логистическия регрессия отработали на этом датасете достаточно плохо и я остановился на support-vector machines (SVM) + stochastic gradient descent (SGD).

```python
    sdgc = SGDClassifier(loss='hinge', penalty='l2', alpha = 0.0001, random_state = 42,max_iter = 5, tol = None)

    text_clf = Pipeline([
        ('vect', CountVectorizer(ngram_range=(1,1))),
        ('tfidf', TfidfTransformer()),
        ('clf', sdgc)])
```
 
1. Baseline на токенах:

**Cross Validation:**
```
[0.31556437 0.32539464 0.33909816 0.34125946 0.30854301]
average 0.32597192855590174
```

**Baseline SVM+SGD:**
```
              precision    recall  f1-score   support

           1       0.43      0.38      0.40       137
           2       0.38      0.05      0.08       107
           3       0.25      0.13      0.17       134
           4       0.35      0.16      0.22       422
           5       0.73      0.92      0.81      1465

    accuracy                           0.66      2265
   macro avg       0.43      0.33      0.34      2265
weighted avg       0.59      0.66      0.60      2265

```

Итерация 1: Комментарии лемматизированы. Показатели стали немного лучше.

```
[0.32732332 0.34757932 0.32927823 0.32328426 0.3258048 ]
average 0.33065398500482457
```

**SVM+SGD on lemmas:**
```
              precision    recall  f1-score   support

           1       0.44      0.43      0.44       137
           2       0.15      0.02      0.03       107
           3       0.24      0.11      0.15       134
           4       0.34      0.17      0.22       422
           5       0.73      0.92      0.82      1465

    accuracy                           0.66      2265
   macro avg       0.38      0.33      0.33      2265
weighted avg       0.58      0.66      0.61      2265
```

Итерация 2: добавил биграммы

```
[0.32828907 0.3811926  0.34015904 0.36068777 0.34939128]
average 0.3519439526630803
```

**SVM+SGD on lemmas + bigrams:**
```
              precision    recall  f1-score   support

           1       0.49      0.49      0.49       137
           2       0.14      0.01      0.02       107
           3       0.35      0.13      0.19       134
           4       0.35      0.18      0.24       422
           5       0.73      0.92      0.82      1465

    accuracy                           0.67      2265
   macro avg       0.41      0.35      0.35      2265
weighted avg       0.60      0.67      0.61      2265
```

Итерация 3: удалил часть комментириев с вопросами из исходных данных
```
[0.32430883 0.35799266 0.36434606 0.33397843 0.34868694]
average 0.3458625822865783
              precision    recall  f1-score   support

           1       0.53      0.47      0.50       136
           2       0.12      0.01      0.02       107
           3       0.30      0.11      0.16       133
           4       0.33      0.18      0.23       415
           5       0.73      0.92      0.81      1449

    accuracy                           0.67      2240
   macro avg       0.40      0.34      0.34      2240
weighted avg       0.59      0.67      0.61      2240
```


**Выводы:**
1. Лемматизация не дала ожидаемного значительного прироста качества работы модели на этом наборе данных;
2. Добавление биграмм немного улучшило результат как на леммах, так и на токенах;
3. Очень удобно сохранять и загружать промежуточные результаты, чтобы не пересчитывать их каждый раз;
4. Фильтрация исходных данных от вопросов немного улучшила оценку.
5. Я пробовал приклеить частицы к следующим за ними словам, но это не дало особого эффекта.