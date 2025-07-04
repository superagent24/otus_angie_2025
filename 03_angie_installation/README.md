# ДЗ № 3 // Варианты установки Angie

На VMware ESXi развёрнута ВМ со следующими характеристиками:

ОС Debian 12.2.0

2 vCPU

4 GB RAM

30 GB HDD

VMXNET 3 network adapter

# Установка Angie

1. Установка вспомогательных пакетов для подключения репозитория Angie:
   
   `sudo apt update && sudo apt upgrade
   sudo apt-get install -y ca-certificates curl`
   
2. Скачивание открытого ключа репозитория Angie для проверки подлинности пакетов:
   
   `sudo curl -o /etc/apt/trusted.gpg.d/angie-signing.gpg \
            https://angie.software/keys/angie-signing.gpg`
   
3. Подключение репозитория Angie:
   
   `echo "deb https://download.angie.software/angie/$(. /etc/os-release && echo "$ID/$VERSION_ID $VERSION_CODENAME") main" \
    | sudo tee /etc/apt/sources.list.d/angie.list > /dev/null`

4. Обновление индексов репозиториев:

   `sudo apt update`

5. Установка пакета Angie:

   `sudo apt install -y angie`

6. Установка пакетов сторонних модулей Brotli и Zstandard, а также установка пакета Console Light:

   `sudo apt install -y angie-module-brotli angie-module-zstd angie-console-light`

7. Редактирование файла настроек Angie:

   `sudo nano /etc/angie/angie.conf`

   <details>

   <summary>Содержимое /etc/angie/angie.conf</summary>

```
   user  angie;
worker_processes  auto;
worker_rlimit_nofile 65536;

error_log  /var/log/angie/error.log notice;
pid        /run/angie.pid;

load_module modules/ngx_http_zstd_filter_module.so;
load_module modules/ngx_http_zstd_static_module.so;

load_module modules/ngx_http_brotli_filter_module.so;
load_module modules/ngx_http_brotli_static_module.so;

events {
    worker_connections  65536;
}

http {
    include       /etc/angie/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    log_format extended '$remote_addr - $remote_user [$time_local] "$request" '
                        '$status $body_bytes_sent "$http_referer" rt="$request_time" '
                        '"$http_user_agent" "$http_x_forwarded_for" '
                        'h="$host" sn="$server_name" ru="$request_uri" u="$uri" '
                        'ucs="$upstream_cache_status" ua="$upstream_addr" us="$upstream_status" '
                        'uct="$upstream_connect_time" urt="$upstream_response_time"';

    access_log  /var/log/angie/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;

    #gzip  on;

    include /etc/angie/http.d/*.conf;
}

#stream {
#    include /etc/angie/stream.d/*.conf;
#}
```

   </details>



8. Редактирование файла конфигурации веб-сервера 

/etc/angie/http.d/default.conf



9. Проверка корректности конфигурации Angie и перезапуск Angie:













