# Тестовое задание в Web-карты

Для запуска проекта локально:
1. `npm install`
2. `npm start`

Текущая версия реализации: http://artrozhe-maptest.surge.sh/

Параметр `page_size` в запросе к API выставлен на `15000`.

Первые два уровня задания реализованы полностью. Третий уровень реализован частично.
После реализации первых двух уровней решил, что алгоритм можно вынести в веб-воркер, потом
наткнулся на реализацию https://github.com/2gis/general, использовал подход работы с веб-воркером, 
спасибо за интересный код)

Так как в задании не указан критерий, по которому определяется приоритет маркера, и данные в том виде, в котором приходят,
с сервера так же явно не говорят о критерии определяния приоритета для каждго маркера, алгоритм отталкивается от соглашения, 
что список маркеров приходит с сервера отсортированным в порядке убывания приоритета.

Для определения пересечения следующего маркера с предыдущими используется библиотека rbush. Внутри она использует создание
деревьев, которые позволяют снизить сложность поиска пересечений с квадратичной `O(n*2)` до линейно-логарифмической `O(n*log n)`.

На данный момент локальная фильтрация в классе Map закомментирована, используется вариант работы с
веб-воркером. На собственной машине вариант с веб-воркером работает медленнее, однако, алгоритм
фильтрации маркеров в реальных условиях должен быть сложнее (группировка маркеров, различные иконки, разное поведение для
групп), а так же на странице возможно выполнение большого количество кода в основном потоке. Учитывая это, в дальнейшем 
можно получить выгоду в виде более гладкой анимации и большей отзывчивости интерфейса, так как процесс выполнения 
фильтрации никак не будет влиять на другие процессы на странице.

В плане оптимизации текущего решения, вижу вариант использования `canvas` для отрисовки слоя с маркерами. На данный момент
для каждого маркера создаётся `DOM` элемент, над которым затем производятся манипуляции. Это сильно замедляет работу и 
делает анимацию не такой плавной, какой она может быть. Конечно, возможность использования `canvas` зависит от условий, 
в которых приложение должно работать. Вариант с `DOM` элементом для каждого маркера будет работать даже в очень старых 
браузерах, тогда как поддержка `canvas` присутствует в относительно новых версиях браузеров.

Также, для уменьшения времени работы кода, можно было бы не очищать все маркеры с карты при движении с одинаковым зумом.
Присутствующие маркеры, которые попадают в видимую область карты, должны участвовать в отборе на следующей итерации, а не
удаляться и создаваться заново.
