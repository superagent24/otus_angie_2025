# ДЗ № 3 // Варианты установки Angie

На VMware ESXi развёрнута ВМ со следующими характеристиками:

ОС Debian 12.2.0

2 vCPU

4 GB RAM

30 GB HDD

# Установка Angie

1. Установка вспомогательных пакетов для подключения репозитория Angie:
   
   `sudo apt update && sudo apt upgrade
   sudo apt-get install -y ca-certificates curl`
   
3. Скачивание открытого ключа репозитория Angie для проверки подлинности пакетов:
   
   `sudo curl -o /etc/apt/trusted.gpg.d/angie-signing.gpg \
            https://angie.software/keys/angie-signing.gpg`
   
4. Подключение репозитория Angie:
   
   `echo "deb https://download.angie.software/angie/$(. /etc/os-release && echo "$ID/$VERSION_ID $VERSION_CODENAME") main" \
    | sudo tee /etc/apt/sources.list.d/angie.list > /dev/null`

5. Обновление индексов репозиториев:

   `sudo apt update`

7. Установка пакета Angie:

   `sudo apt install -y angie`
