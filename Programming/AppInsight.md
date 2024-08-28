AppInsight


requests - запросы (активити - url пустой)
traces - что мы пишем в логи
dependencies - если, например, core сервис пошел в базу. Походы в сторонние ресурсы в рамках запроса. Запись будет сопоставлена с другим запросом. Сопоставление происходит по паре id -> operation_ParentId. Если идем bff -> core, то operation_ParentId - bff, id - core
exceptions - ошибки. Интересует InnermostMessage - внутренняя ошибка

has 	и contains - appins рекомендует has (он быстрее), но при этом has не ищет по куску слова, а contains ищет

order by - всегда надо использовать asc/desc, если не будет, то ошибки не будет, но сортировка не применится

Можно визуализирвоать в Transaction Search по найденному request id. Тут можно смотреть запросы, подзапросы и трейсы. Но он тащит всю информацию по operation id, так что может быть слишком много сторонней информации

Получение логов функций

Для бэка все просто - operation_parent_id от трейса = id от реквеста
Для функций это не работает. В функциях нет привязки по id, так что там ищем запрос, смотрим время, ищем трейсы по времени и пытаешься понять, что с чем связано

cloud_RoleName - название сервиса в кубере для бэкенда. Для функций это можем написать или мы, или рантайм
cloud_RoleInstance
если web и backend, то это бэк и привязка работает
Если func, то это частичные логи от функций (но тут еще могут быть имена другой машинки, они меняются). Если название без четкого префикса, то это функции

Пример - берем трейсы за определенное время и делаем union с экспешионами

traces
| where timestamp between (todatetime('2024-02-13T22:50:43.000Z') .. todatetime('2024-02-13T22:50:46.000Z'))
| project timestamp, messages, type = 'trace' <- выбираем нужные колонки
| union (
 	expeptions
	| where …
	| project …, type = 'error'
)
| order by timestamp desc

Пример дебага функций

Получаем запросы, которые делала активити:

requests
| where url has 'lot_management/update_containers'
| order by timestamp desc

Видим много одинаковых запросов с 404, bерем первый сверху, у него id =x
Его ошибки:

exceptions
| where operation_ParentId == 'x'

Его вложенные запросы:

dependencies
| where operation_ParentId == 'x'
| order by timestamp desc

Видим 404 в container_process. Берем id, находим сам запрос:

requests
| where operation_ParentId == 'x'

Теперь берем уже его id и получаем его ошибки:

exceptions
| where operation_ParentId == 'x'
| order by timestamp desc

Если есть доступ к продовому AppInsights то можно найти ошибку следующим запросам в логи:

requests
| where url has '/finish'
| join kind=inner exceptions
    on $left.id == $right.operation_ParentId
| project timestamp, url, innermostMessage
| order by timestamp desc

