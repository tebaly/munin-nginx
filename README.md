# Munin NGINX request_time

Плагины статистики по времени обработки запроса NGINX для мунин.

## Обязательно

Настроить формат лога nginx для работы плагина и перезагрузить nginx. По умолчанию, информация о времени НЕ записывается в логи, это поведение необходимо предварительно сконфигурировать.
```
log_format timed_combined '$remote_addr - $remote_user [$time_local] '
    '"$request" $status $body_bytes_sent '
    '"$http_referer" "$http_user_agent"'
    'rt=$request_time';

access_log /var/log/nginx/access.log timed_combined;
```
> service nginx reload

## Важно
Обрабатываются как успешные 200 так и разрывы соединения 499 - ошибка NGINX когда пользователь не дождался ответа сервера. В других реализациях плагина эти строки(499) игнорируюстя, но они несут непосредственную важность.

Плагин описываемый в [статье](https://letsclearitup.com.ua/debian/pishem-svoy-plagin-dlya-munin-nginx-request-time.html) работать не будет. Тот код либо устарел, либо изначально опубликован с ошибками, либо выложен некорректно.
