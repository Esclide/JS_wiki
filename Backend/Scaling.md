Масштабирвоание

Горизонтальное - делаем кучу реплик одного и того же сервиса

Вертикальное - увеличиваем количество ресурсов для одного и того же сервиса

Репликация - если все трудно на чтение, мы делаем репликацию, савим индекс и все становится лучше ( полное копирование БД на другой сервер)

Если надо ускорить запись и у нас много данных и надо достучаться до определенного - шардирование (разбиваем записи по разным серверам)

Партиционирование - само хранилище разбивает данные для более быстрого доступа , клиент про это не знает и ходит за данными в одно место. Прочитать про ClickHouse - надо создать ключ так, чтобы все данные были разбиты на партиции и все запросы летели в отдельные партиции, можно параллельно. Если ключ подобран неправильно, то может запрос лететь во все партиции

Шардирование - когда данные хранятся в разных инстансах и клиент об этом знает и ходит за данными в разные места


