# Munin nginx request_time

Плагины статистики по времени обработки запроса nginx для мунин. Написаны по  [статье](https://letsclearitup.com.ua/debian/pishem-svoy-plagin-dlya-munin-nginx-request-time.html), так как автор видимо решил не публиковать плагины в виде готового кода.

# Обязательно

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

В оригинале статьи допущено ряд ошибок, из-за которых ваш плагин(по описанию в статье) вероятно не запустится. А именно
```
  echo "graph_category nginx
  graph_title Nginx request time
  graph_vlabel %
  graph_scale no
  graph_args --base 1000 -r --lower-limit 0 --upper-limit 100
  t0.label less 50ms
  t0.draw AREA
  t0.type GAUGE
  t0.min 0
  t1.label less 100ms
  t1.draw STACK
  t1.type GAUGE
  t1.min 0
  t2.label less 1s
  t2.draw STACK
  t2.type GAUGE
  t2.min 0
  t3.label more 1s
  t3.draw STACK
  t3.type GAUGE
  t3.min 0
  ";
```

Каждый перенос строки должен завершаться символом новой строки "\n". Как выяснилось, это не всё. Каждая строка типа `t2.label less` не должна начинаться с пробелов, а в статье именно так, что приведет к ошибкам получения данных сервером ` returned no data for label` так как все эти пробелы будут добавлены к метке файла RRD. Я разнес всё в отдельные конструкции echo, и плагин заработал.

Кроме прочего, автор добавил `$request_time` в середину дааных лога, что крайне не желательно, так как может поломать другие скрипты, которые парсят лог nginx. Далее, сам лог может быть уже настроен по официальной справке https://www.nginx.com/blog/using-nginx-logging-for-application-performance-monitoring/ где параметр добавлен в конце строки.
