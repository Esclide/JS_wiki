Stream и Buffer

Buffer - временное хранилище, предназначенное для хранения курка данных, передающихся из одного места в другое. Если скачиваемый или отправляемый файл достаточно большого размера, то его при отправке/получении мы и разбиваем на буферы

Stream - весь механизм передачи данных.

Основная идея - как можно быстрее передать данные, чтобы пользователь мог начать работать с ними еще до того, как они все загрузятся

Readable - читающий

Writable - для записи

Duplex - и чтение, и запись

Transform - дополнительно можем изменять читаемые или записываемые данные (например, сжатие)

pipe - чтение из одного стрима и передача в другой (readStream.pipe(writeStream))
