# ДЗ № 3 // Варианты установки Angie

## Характеристики тестового стенда

На VMware ESXi развёрнута ВМ со следующими характеристиками:

ОС Debian 12.2.0

2 vCPU

4 GB RAM

30 GB HDD

VMXNET 3 network adapter

## Пакетная установка Angie под Debian 12

### 01. Установка вспомогательных пакетов для подключения репозитория Angie:
   
   `sudo apt update -y && sudo apt upgrade -y`
   
   `sudo apt-get install -y ca-certificates curl`
   
### 02. Скачивание открытого ключа репозитория Angie для проверки подлинности пакетов:
   
   `sudo curl -o /etc/apt/trusted.gpg.d/angie-signing.gpg \
   https://angie.software/keys/angie-signing.gpg`
   
### 03. Подключение репозитория Angie:
   
   `echo "deb https://download.angie.software/angie/$(. /etc/os-release && echo "$ID/$VERSION_ID $VERSION_CODENAME") main" \
   | sudo tee /etc/apt/sources.list.d/angie.list > /dev/null`

### 04. Обновление индексов репозиториев:

   `sudo apt update`

### 05. Установка пакета Angie:

   `sudo apt install -y angie`

### 06. Установка пакетов сторонних модулей Brotli и Zstandard, а также установка пакета Console Light:

   `sudo apt install -y angie-module-brotli angie-module-zstd angie-console-light`

### 07. Редактирование файла настроек Angie:

   `sudo nano /etc/angie/angie.conf`

<details>

<summary>Содержимое angie.conf после редактирования</summary>

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

### 08. Редактирование файла конфигурации веб-сервера:

   `sudo nano /etc/angie/http.d/default.conf`

<details>

<summary>Содержимое default.conf после редактирования</summary>

```
server {
    listen       80;
    server_name  daleedalee.ru www.daleedalee.ru localhost;

    #access_log  /var/log/angie/host.access.log  main;

    location / {
        root   /usr/share/angie/html;
        index  index.html index.htm;
    }

    location /status/ {
        api     /status/;
        allow   127.0.0.1;
        deny    all;
    }

    location /console/ {
        # define list of trusted hosts or networks
        allow 127.0.0.1;
        # allow 192.168.0.0/16;
        # allow 10.0.0.0/8;
        deny all;

        auto_redirect on;

        alias /usr/share/angie-console-light/html/;
        index index.html;

        location /console/api/ {
            api /status/;
        }

        # uncomment below lines to enable writable API
        # location /console/api/config/ {
        #     api /config/;
        # }
    }

    #error_page  404              /404.html;

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/angie/html;
    }

    # proxy the PHP scripts to Apache listening on 127.0.0.1:80
    #
    #location ~ \.php$ {
    #    proxy_pass   http://127.0.0.1;
    #}

    # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
    #
    #location ~ \.php$ {
    #    root           html;
    #    fastcgi_pass   127.0.0.1:9000;
    #    fastcgi_index  index.php;
    #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
    #    include        fastcgi_params;
    #}

    # deny access to .htaccess files, if Apache's document root
    # concurs with angie's one
    #
    #location ~ /\.ht {
    #    deny  all;
    #}
}
```

</details>

### 09. Проверка корректности конфигурации Angie и перезапуск Angie:

   `sudo angie -t && sudo systemctl reload angie`

### 10. Проверка корректности работы Angie:

   `sudo systemctl status angie`

${\textsf{\color{lightgreen}●}}$ angie.service - Angie - high performance web server</br>
     Loaded: loaded (/lib/systemd/system/angie.service; ${\textsf{\color{lightgreen}enabled}}$; preset: ${\textsf{\color{lightgreen}enabled}}$)</br>
     Active: ${\textsf{\color{lightgreen}active (running)}}$ since Wed 2025-06-25 14:31:18 MSK; 1 week 2 days ago

### 11. Проверка корректности работы Angie:

   `curl localhost`

<details>

<summary>Результат выполнения команды</summary>

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to Angie!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to Angie!</h1>
<p>If you see this page, the Angie web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="https://en.angie.software/">angie.software</a>.</p>

<p><em>Thank you for using Angie.</em></p>
</body>
</html>
```

</details>

### 12. Проверка корректности работы пакета Console Light:

   `curl -L -I localhost/console`

<details>

<summary>Результат выполнения команды</summary>

```
HTTP/1.1 301 Moved Permanently
Server: Angie/1.9.1
Date: Tue, 08 Jul 2025 11:27:32 GMT
Content-Type: text/html
Content-Length: 168
Location: http://localhost/console/
Connection: keep-alive

HTTP/1.1 200 OK
Server: Angie/1.9.1
Date: Tue, 08 Jul 2025 11:27:32 GMT
Content-Type: text/html
Content-Length: 421448
Last-Modified: Mon, 07 Apr 2025 12:14:07 GMT
Connection: keep-alive
ETag: "67f3c18f-66e48"
Accept-Ranges: bytes
```

</details>

## Установка Angie из Docker образа на основе Debian 12

### 01. Установка Docker и пакета cURL:
   
   `sudo apt update -y && sudo apt upgrade -y`
   
   `sudo apt install -y docker.io curl`

### 02. Добавление службы Docker в автоматический запуск при загрузке системы:
   
   `sudo systemctl enable docker`

### 03. Просмотр состояния службы Docker:
   
   `sudo systemctl status docker`

### 04. Запуск Docker образа Angie:
   
   `sudo docker run -d --restart always --name angie -p 80:80 -v \
   /var/www/html:/usr/share/angie/html docker.angie.software/angie:1.10.0-debian`

> [!TIP]
> 
> Команда `run` запускает контейнер из образа, при этом, если образ отсутствует в системе, будет произведена его загрузка;
>
> Ключ `-d` означает, что контейнер будет запущен в фоновом режиме;
>
> Опция `--restart always` указывает на то, что контейнер должен автоматически перезапускаться в случае его остановки, если только он не был остановлен пользователем;
>
> `--name angie` означает, что создаваемый контейнер будет называться `angie`;
>
> Опция `-p 80:80` используется для проброса портов между контейнером и хост-системой, то есть порт 80 внутри контейнера будет привязан к порту 80 на хостовой системе;
>
> `-v /var/www/html:/usr/share/angie/html` означает, что каталог `/var/www/html` на хост-системе будет смонтирован к каталогу внутри контейнера `/usr/share/angie/html`;
>
> `docker.angie.software/angie:1.10.0-debian` – образ указанной версии Angie на основе Debian 12

### 05. Вывод списка установленных Docker образов:
   
   `sudo docker images`

<details>

<summary>Результат выполнения команды</summary>

```
wwwwwwwwwww
```

</details>

### 06. Вывод списка запущенных контейнеров Docker:
   
   `sudo docker ps`

<details>

<summary>Результат выполнения команды</summary>

```
wwwwwwwwwww
```

</details>

### 07. Создание файла index.html в папке /var/www/html/ :

   `sudo nano /var/www/html/index.html`

<details>

<summary>Содержимое файла index.html</summary>

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to Angie!</title>
</head>
<body>
<h1>Welcome to Angie!</h1>
<p>If you see this page, the Angie web server is successfully installed and
working. Further configuration is required.</p>
</body>
</html>
```

</details>

### 08. Проверка корректности работы Angie:
   
   `curl -I localhost`

<details>

<summary>Результат выполнения команды</summary>

```
wwwwwwwwwww
```

</details>






