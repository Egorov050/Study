Хороший способ недорого протестить любую гипотезу. 
Например , у нас есть гипотеза , что купоны это нужная вещь для пользователя. Однако реализовать все систему купонов - дорого. Поэтому мы просто разрабатываем окошко для ввода купона без бекенда и ставим счетчик на клики. Если кто то вводит какой - то купон , мы выведем плашку по типу `Упссс , такого купона нет...` но при этом мы поймем , что пользователь был заинтересован. 

Но у такого способа есть минус. Это негативный клиентский опыт. Пользователей от нажатия ожидает определенных действий , но взамен ему вылетает ошибка , пусть она и красиво обработана. Поэтому нам нужно испытывать это только на определенных клиентов ( вспомним RFM анализ ). 

Почитать : https://habr.com/ru/articles/436716/