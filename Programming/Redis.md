Redis


Redis - это опенсорсный сервер баз данных типа ключ-значение. Иными словами, это сервер структур данных. Вместо того, чтобы хранить данные в таблицах, здесь данные хранятся в структурах, нужных прграммисту

Типы данных:
* Строка (String)
* Битовый массив (Bitmap) - 0001010
* Битовое поле (Bitfield) - {4324}{3245}{213}
* Хеш-таблица (Hash) - {a: hello, b: world}
* Список (List) - [A>B>C]
* Множество (Set) - [A<B<C]
* Упорядоченное множество (Sorted set) - {A: 1, B: 2, C: 3}
* Геопространственные данные (Geospatial) - {A: (23.1, 0.5)}
* Структура HyperLogLog (HyperLogLog) - 00100110 01101111
* Поток (Stream)

Redis — это база данных, размещаемая в памяти, которая используется, в основном, в роли кеша, находящегося перед другой, «настоящей» базой данных, вроде MySQL или PostgreSQL. Кеш, основанный на Redis, помогает улучшить производительность приложений.

Примеры таких данных могут включать в себя сессионные кеши или кеши данных, а так же содержимое панелей управления — вроде списков лидеров и отчётов, включающих в себя данные, агрегированные из разных источников.

Redis — это однопоточная система

Redis сохраняется на диск, но он не сохраняет данные синхронно с тем как вы записываете их. Есть две причины из-за которых Redis в качестве главного хранилища — отстой:
— Вы вынуждены умещать все свои данные в памяти, и…
— Если сервер откажет между двумя синхронизациями с диском — вы потеряете всё что сидело в памяти.





