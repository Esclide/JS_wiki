WeakMap/WeakSet

Отличие от обычных Map и Set - если объект хранится только в weakmap (как ключ) или weakset, то он очищается сборщиком мусора в любой момент

Используется, когда надо удалять вспомогательные данные при удалении основного объекта

* 		Только объекты в качестве ключей.
* 		Нет свойства size.
* 		Нельзя перебрать элементы итератором или forEach.
* 		Нет метода clear().

Мы в принципе не можем получить список всех элементов, так как они могут быть собраны ГК в любой момент. Нет какого-то конкретного момента, когда такая очистка точно произойдёт – это определяется внутренними алгоритмами сборщика и его сведениями о системе.

Используется как memory cash. Реверанс в сторону плюсов
