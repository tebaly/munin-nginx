# Munin nginx request_time

Плагины статистики по времени обработки запроса nginx для мунин.

## Обязательно

Настроить формат лога nginx для работы плагина и перезагрузить nginx
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
