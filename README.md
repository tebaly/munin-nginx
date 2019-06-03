# Munin NGINX 

Плагины статистики по времени обработки запроса NGINX для Munin. Основаны на обработке единственного параметра из лога доступа. По умолчанию, информация о времени _не записывается в логи_, это поведение необходимо предварительно настроить. 

`$request_time` - время обработки запроса в секундах с точностью до миллисекунд; время, прошедшее с момента чтения первых байт от клиента до момента записи в лог после отправки последних байт клиенту.  Doc: [ngx_http_log_module](http://nginx.org/ru/docs/http/ngx_http_log_module.html)

## Обязательно

Настроить формат лога nginx для работы плагина и перезагрузить nginx. В конфигурации NGINX всегда существует предопределённый формат _[combined](http://nginx.org/ru/docs/http/ngx_http_log_module.html#log_format)_, необходимо дописать в конец единственный параметр `rt=$request_time`
```
log_format main '$remote_addr - $remote_user [$time_local] '
    '"$request" $status $body_bytes_sent '
    '"$http_referer" "$http_user_agent"'
    'rt=$request_time';

access_log /var/log/nginx/access.log main;
```
> service nginx reload

## Важно

Обрабатываются как успешные 200 так и разрывы соединения 499 - ошибка NGINX когда пользователь не дождался ответа сервера. В других реализациях плагина эти строки(499) игнорируюстя, но они несут непосредственную важность.

Плагин описываемый в [статье](https://letsclearitup.com.ua/debian/pishem-svoy-plagin-dlya-munin-nginx-request-time.html) работать не будет. Тот код либо устарел, либо изначально опубликован с ошибками, либо выложен некорректно.

---

### nginx_request_time

Формирует график по времени успешного (код **200**) запроса. Менее 0.05 / 0.10 / 0.25 / 0.5 / 0.75 / 1 / 1.5 / 2 секунды
Менее 3 и более 3 секунд. В последнюю ментрику попадают все запросы дольше трех секунд.

### nginx_request_time_freq

Тоже самое, только включает также запросы завершившихся **499** кодом. Менее 0.05 / 0.1 / 0.5 секунды.
Менее 1 и более 1 секунд. В последнюю ментрику попадают все запросы дольше одной секунд.

### nginx_client_close

Статисика прерванных запросов завершившихся 499 кодом. Специфический код ошибки для Nginx означающий, что клиент закрыл соединение не дождавшись ответа сервера (client closed the connection). Это может быть таймаутом на стороне клиента. 
