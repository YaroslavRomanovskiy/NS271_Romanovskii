# Характеризация фемтосекундных лазерных импульсов ближнего инфракрасного диапазона методами машинного обучения
Для восстановления фазы и длительности фемтосекундных лазерных импульсов используются методы машинного обучения, основанные на нелинейных изменениях спектра при фазовой самомодуляции. Для обучения нейросетевых моделей используются смоделированные данные распространения импульсов в стеклянной пластине. Обученная на результатах численного моделирования нейросеть восстановливает фазу и временную огибающую импульса из экспериментальных данных.

## Введение

В экспериментах для достижения наименьшей длительности и наибольшей пиковой интенсивности фемтосекундного импульса требуется минимизация фазовой задержки между его различными спектральными компонентами. Для контроля временной структуры лазерных импульсов необходима характеризация амплитуды и фазы. При распространении лазерного импульса в нелинейной среде из-за эффекта Керра показатель преломления становится нелинейным и зависит от интенсивности импульса n=n0+n2I, где n0 – линейная часть показателя преломления, n2 – нелинейная, I – интенсивность импульса. Импульс, испытывая ФСМ, приобретает фазовый набег, а форма его спектра меняется. Спектральные изменения между исходным спектром импульса и спектром ФСМ после прохождения через нелинейную среду напрямую связаны с исходным временным профилем интенсивности, поэтому эта нелинейная связь может быть использована для получения информации об исходной фазе импульса. Влияние ФСМ на сверхкороткий импульс при прохождении через среду может быть смоделировано при помощи численного решения обобщённого нелинейного уравнения Шрёдингера [1]. 
Использование алгоритмов машинного обучения для восстановления спектральной фазы и характеризации импульса может заменить итеративные алгоритмы и численное моделирование, позволяя осуществлять мониторинг фазы и временной огибающей лазерного импульса в режиме реального времени [2]. На рис. 1 показана схема работы методики восстановления временного профиля лазерного импульса при помощи явления ФСМ и полносвязной нейронной сети: зарегистрировав исходный спектр лазерных импульсов и спектр ФСМ после прохождения через стеклянную пластину, предварительно обученная на смоделированных данных нейросеть восстанавливает фазу исходного импульса. Далее, применив восстановленную моделью фазу к исходному спектру, после преобразования Фурье восстанавливается временная огибающая и значение длительности исходного импульса.


![Схема](https://s93klg.storage.yandex.net/rdisk/3216849c12be1faca8abb6939335bc78b07f5071824dc14950dfd62eba1574ac/6744adbd/fKqInKw3d7bLFOeFnMGnhJ4O4-IsEsHy-O5CT_dZYFlX5rfpGlOTm96zleuLbE7cu2Mv67avdnkGMnO4Ifm7phbJBwPT8FHVko_MDshyWNar8npumZHI4midPdWhecNq?uid=1130000056333972&filename=RNF_NN.png&disposition=inline&hash=&limit=0&content_type=image%2Fpng&owner_uid=1130000056333972&fsize=138465&hid=0541b6773bfb5273554de5749e9f0c66&media_type=image&tknv=v2&etag=6d9faa1ec5b5e8f1d4535314c72966cc&ts=627bfb4092940&s=515b1b2528ef6870399fbeaaff7bd328f6f1989f867d0ade4b7fe231b14a36d3&pb=U2FsdGVkX1-4WuVWeNh4_AJfrKN9g8SiDx_fy_IzHvjsI5IG12i5UgQiN9E4m2yw5va9SaFh3nXXLpuGgClwFiIiFQP7sdEjJtO2UNKnZkCWkoz3puUDQhwRpBtGAHj0)

*Рис. 1.* Схема для восстановления временного профиля импульса при помощи фазовой самомодуляции. Импульс с исходным спектром проходит через нелинейную среду и испытывает эффект Керра, вызывающий изменения в спектре. Нейросеть, получив на входе исходный спектр и спектр после ФСМ, предсказывает фазу исходного импульса, позволяя восстановить его временной профиль

В этой работе рассматривается применение методики восстановления спектральной фазы и временного профиля при помощи машинного обучения для фемтосекундных импульсов системы OPA (optical parametric amplification, оптическое параметрическое усиление) ближнего ИК-диапазона на длине волны 1.03 мкм [3]. 

## Данные

Для генерации датасета для обучения нейросети используется численное моделирование прохождения импульсов с различными начальными параметрами через нелинейную среду при помощи библиотеки PyNLO [1]. PyNLO позволяет получить численное решение одномерного обобщенного нестационарного уравнения Шрёдингера. Для оптимизации вычислений при генерации датасета в сервисе Яндекс Датасфера были реализованы параллельные вычисления при помощи библиотеки multiprocessing, был сгенерирован датасет на 202 000 импульсов.   
Разбиение на train-val-test в соотношении 0.825-0.125-0.05. 

## Модель

В качестве модели используется полносвязная нейросеть (архитектура на схеме ниже), пример её работы есть в папке notebooks в блокноте Model_for_reference_GitHub.ipynb.

![image](https://github.com/user-attachments/assets/7c0d2a8c-7dad-45b0-9b71-404467965ecd)


Гиперпараметры:
- *Оптимизатор* AdamW( lr = 0.001,  gamma=0.9)
- *Функция потерь* MSELoss
- batch_size = 256
- lr_scheduler.ReduceLROnPlateau

Метрики качества:
- Ошибка восстановления пиковой интенсивности (используется для оценки качества работы модели во время обучения как целевая метрика)
- Ошибка восстановления длительности FWHM (Full Width Half Maximum) импульса

![Схема](https://s719sas.storage.yandex.net/rdisk/6958b1a54c413a9ce4fd2ad73badb1b2fb6a562b894f993d5c76f51ee4076513/674540cc/fKqInKw3d7bLFOeFnMGnhJM_jWgTkIX8x7NLEwKjp1VlOR9X4PprydGNp2CxilIlPUaXlrU7dJkUQSePUzw6Ac0f4Q69HSauiTWHLXPA-eer8npumZHI4midPdWhecNq?uid=1130000056333972&filename=%D0%A1%D0%BD%D0%B8%D0%BC%D0%BE%D0%BA%20%D1%8D%D0%BA%D1%80%D0%B0%D0%BD%D0%B0%202024-11-26%20022958.jpg&disposition=inline&hash=&limit=0&content_type=image%2Fjpeg&owner_uid=1130000056333972&fsize=55325&hid=ffd7595d3265c906bcda530681dcc8ac&media_type=image&tknv=v2&etag=ee38c7afd5a52301a354dbcd7f3f5b61&ts=627c877f8cb00&s=3f01d7cc6d9f909377520b53d4f13910a13ef0e5754fc5a1cfc7379788ce1c91&pb=U2FsdGVkX1_6iaaDJybCJDjJn6E_a9o6bCaZHQq0ux-FIeKrwKPhK1Eoi4fK5OxAOdkXzYS2hrbBS7PNVfJ8v7zLJKm-cqtGwj9gdYeq80i72OHeTNOJqSjdgya9Cu2y)
Loss и целевая метрика (ошибка восстановления пиковой интенсивности) на тренировочном и тестовом датасете.
## Результаты на тестовом датасете

Пример восстановления фазы моделью (оранжевая прерывистая) на тестовом датасете:

![image](https://github.com/user-attachments/assets/1d19fe25-ef6a-450d-aea6-fb857fe58d13)

И распределение ошибки пиковой интенсивности и длительности по тестовому датасету:
![image](https://github.com/user-attachments/assets/4911cc33-1bb6-426d-9b3a-d7ad9a2a4d9f)
FWHM: 4.9% среднее, станд. откл. 9.4%
Пиковая интенсивность: 3.2% среднее, станд. откл. 4.9%

## Результаты на экспериментальных данных

При проверке на реальных данных OPA-системы референсное значение исходной фазы и длительности определялось методом SHG-FROG.
![image](https://github.com/user-attachments/assets/c0801571-4fbb-4722-b377-096bc51709c8)

Таким образом, модель восстановила длительность 217 фс, а методика SHG-FROG 213 фс. 

## Список литературы
[1] https://github.com/pyNLO/PyNLO  
[2] Stanfield, Matthew, et al. "Real-time reconstruction of high energy, ultrafast laser pulses using deep learning." Scientific reports 12.1 (2022): 5299.  
[3] Mitrofanov A. V. et al. Post-filament self-trapping of ultrashort laser pulses //Optics Letters. – 2014. – Т. 39. – №. 16. – С. 4659-4662.

## Проверка кода модели

Вы можете выполнить блокнот **Model_for_reference_GitHub.ipynb** (например, в Google Colab или Яндекс Датасфере) из папки **notebooks** для получения основных результатов этого файла.

Этот проект был выполнен на **JupyterLab** в сервисе **Яндекс Датасфера**.

## Примеры

В папке **examples** Вы можете выполнить файл **Example_to_Plot_results.ipynb**, чтобы увидеть пример работы модели на графиках на элементах из тестового датасета.


