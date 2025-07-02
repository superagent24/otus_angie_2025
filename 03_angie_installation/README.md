# Варианты установки Angie // ДЗ № 3

На VMware ESXi развёрнута ВМ:

ОС: Debian 12.2.0
vCPU: 2
RAM: 4 GB
HDD: 30 GB

Установка Angie

1. `sudo apt update && sudo apt upgrade`
2. `sudo apt-get install -y ca-certificates curl`
3.  `sudo curl -o /etc/apt/trusted.gpg.d/angie-signing.gpg \
            https://angie.software/keys/angie-signing.gpg`
