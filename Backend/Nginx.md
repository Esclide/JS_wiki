Nginx

Nginx - это HTTP прокси сервер, используемый для повышения производительности приложения как

- Обратный прокси сервер - для повышения производительности по мере увеличения трафика для балансировки нагрузки между серверами. Это основной вариант использования Nginx в приложениях Node.js.
- Load balancing - повышает производительность и одновременно снижает нагрузку на серверные службы за счет отправки клиентских запросов для выполнения любым сервером, имеющим доступ к запрошенному файлу.
- Кэширование статического контента
- Мониторинг производительности



Nginx currently supports seven scripting languages: Go, Node.js, Perl, PHP, Python, Ruby, and Java Servlet Containers

Конфигурация

http {
    server {
	listen       8080; - какой порт слушаем
	server_name 		wach.quest; - какой url слушаем

	location / { - указываем, какой префикс пути в запросе будет обрабатываться (/, /images/, etc)
            client_max_body_size 32M;
            proxy_read_timeout 3600;
            proxy_pass http://backend:${APP_LISTEN_PORT};  - куда перенаправляем
        }

    }
}

Может содержать несколько server блоков, слушающих разные порты или url.

Предоставление статического контента

server {
    location / {
        root /data/www;
    }

    location /images/ {
        root /data;
    }
}

Тут запросы на http://localhost/images получают данные из /data/images, а на http://localhost/ - из /data/www

Натройка прокси сервера

