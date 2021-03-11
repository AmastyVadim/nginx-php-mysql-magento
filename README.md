# Docker контейнер для локальной разработки на Magento 2
В этом репозитории хранится специфично настроеный docker контейнер для работы с Magento 2  
Состав контейнера:
- nginx
- php 7.4
- mysql 5.7
- phpmyadmin
- elasticsearch 7.4
- xdebug-2.9.8
### Подготовка
Собственно нужен докер)  
**MacOS:**

[Docker](https://docs.docker.com/docker-for-mac/install/), [Docker-compose](https://docs.docker.com/compose/install/#install-compose) и [Docker-sync](https://github.com/EugenMayer/docker-sync/wiki/docker-sync-on-OSX).

**Windows:**

[Docker](https://docs.docker.com/docker-for-windows/install/), [Docker-compose](https://docs.docker.com/compose/install/#install-compose) и [Docker-sync](https://github.com/EugenMayer/docker-sync/wiki/docker-sync-on-Windows).

**Linux:**

[Docker](https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/) и [Docker-compose](https://docs.docker.com/compose/install/#install-compose).

Далее нужно запустить службу докера  
`sudo systemctl enable docker.service`

Чтобы не запускать докер от рутового пользователя в дистрибутивах linux, можно воспользоваться [этим](https://docs.docker.com/engine/install/linux-postinstall/) гайдом.  
Если вкратце, то запускаем следующие команды:
```
sudo groupadd docker
sudo usermod -aG docker $USER
newgrp docker 

```

### Разворачивание контейнера
1. Клонируем репозиторий в домашнюю папку  
`git clone https://github.com/AmastyVadim/nginx-php-mysql-magento.git` 

2. Переходим в папку с репозиторием и копируем .env-example в .env файл с переменными  
`cp .env-example .env`  
Переменные у нас следующие:  
*USER_ID*=ваш **uid**   
*GROUP_ID*=ваш **gid**  
если не знаете что тут писать, оставьте 1000.

3. Прописываем узел magento.loc в файле /etc/hosts  
`echo "127.0.0.1 magento.loc" | sudo tee -a /etc/hosts`

4. Выставляем права на выполнение для наших скриптов:  
`chmod +x ./stop && chmod +x ./start && chmod +x ./restart`

5. Запускаем контейнер и ждём, пока он скомпилируется:  
`./start`

### Настраиваем дебаггер для PhpStorm.

P/S исходники брались отсюда: https://habr.com/ru/post/473184/

Тут и настройка PhpStorm под Xdebug

Скачиваем и устанавливаем магенту:

https://www.dropbox.com/s/nn1r6ybtncdbhpc/magento-ce-2.4.1_sample_data-2020-09-25-04-46-06.zip?dl=0

php bin/magento setup:install --base-url=http://ap74.magento.loc/ --db-host=mysql --db-name=m2db --db-user=root --db-password=secret --admin-firstname=admin --admin-lastname=admin --admin-email=admin@example.com --admin-user=admin --admin-password=a111111 --language=en_US --currency=USD --timezone=America/Chicago --cleanup-database --sales-order-increment-prefix="ORD$" --session-save=db --use-rewrites=1 --search-engine=elasticsearch7 --elasticsearch-host=es7.4 --elasticsearch-port=9200