Архитектура Node js

Нода - это платформа, где множество составных частей, но самое главное - v8 и libuv. v8 - движок, там  нас общение с CPU, C/C++ библиотеки, суть в том, что нода - это платформа между JS и C++, а для обработки I/O запросов у нас есть libuv, где как раз реализован eventLoop. А v8 - это про преобразование в машинный код

￼

1. Пользовательский код: код приложения на JavaScript, написанный программистами.
2. Node.js API: список методов, предлагаемых Node, которые могут быть использованы в пользовательском коде
3. Привязки и дополнения C++: Читая о Node.js, вы видите, что V8 написан на C++, Libuv написан на C и так далее. В основном все модули написаны либо на C, либо на C++. Bidings действуют как связующее звено между двумя слоями, поэтому Node.js может беспрепятственно использовать низкоуровневый код, написанный на C или C++
4. Зависимости Node.js: Этот уровень представляет низкоуровневые библиотеки, которые использует узел. Самые большие зависимости — это движок Google V8 и Libuv.
5. Операционная система: это самый низкий уровень, который представляет OS API (системные вызовы - syscalls), используемые упомянутыми выше библиотеками. Поскольку операционные системы разные, библиотеки включают реализации как для Windows, так и для Unix-версий, которые делают Node.js независимым от платформы.



Libuv - это библиотека, написанная на C, которая используется для абстрактных неблокирующих операций ввода-вывода. Он предлагает следующие функции:
* Цикл событий (Event Loop)
* Асинхронные сокеты TCP и UDP
* Асинхронное разрешение DNS
* Асинхронные операции с файлами и файловой системой
* Пул потоков (Thread Pool)
* Дочерние процессы (Child processes)
* Часы с высоким разрешением
* Примитивы потоковой передачи и синхронизации
* Опрос (Polling)
* Потоковая передача (Streaming)
* Трубы (Pipes)
V8 - это библиотека, которая предоставляет Node.js свой движок JavaScript. А именно JIT (Just-in-time) компилятор. Он непрерывно переключается между компиляцией и выполнением блоков кода. Преимущество чередования компиляции и запуска заключается в том, что он может собирать информацию во время выполнения кода и на основе полученных данных строить предположения о том, что произойдет в будущем. Эти предположения полезны для оптимизации кода.


Минусы микросервисов

Основной - начинаются проблемы с транзакциями. Варианты - двухфазная фиксация (но тут проблема - узкое горлышко и долго исполняется)
Возможность сетевых проблем

Выбор БД на проекте

Надо исходить из требований. Если много неструктурированных данных - монго, если важна надержность, чтобы данные не потерялись - постгрес,
