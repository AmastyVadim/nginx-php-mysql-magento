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
1. Клонируем репозиторий в домашнюю/(любую удобную) папку   
    `git clone https://github.com/AmastyVadim/nginx-php-mysql-magento.git` 

1. Переходим в папку с репозиторием и копируем .env-example в .env 
    `cp .env-example .env`  
    Переменные у нас следующие:  
    *USER_ID*=ваш **uid**   
    *GROUP_ID*=ваш **gid**  
    Чтобы их правильно заполнить, выполняем команду `id`, вывод получится примерно такой:  
    ```
    $ id
    uid=1000(<user_name>) gid=1000(<group_name>) groups=1000...
    ```
    Берём **uid**, **gid** и подставляем в наш *.env*  

1. Прописываем узел magento.loc в файле /etc/hosts  
    `echo "127.0.0.1 magento.loc" | sudo tee -a /etc/hosts`

1. Выставляем права на выполнение для наших скриптов:  
    `chmod +x ./stop && chmod +x ./start && chmod +x ./restart`

1. Запускаем контейнер. Первый запуск будет долгим, т.к. докер выкачивает нужные образы и компилирует их:  
    `./start`

### Устанавливаем Маgento 2
1. Скачиваем [архив](https://www.dropbox.com/s/nn1r6ybtncdbhpc/magento-ce-2.4.1_sample_data-2020-09-25-04-46-06.zip?dl=0)
1. Распаковываем его в папку `www/magento.loc`
1. Правим права на файлы:  
    `sudo chmod -R 777 www/magento.loc/*`
1. Создаём в phpmyadmin бд.
    1. Переходим в браузере по адресу 0.0.0.0:2200
    1. Логинимся как `root`, пароль `secret`
    1. Базу данных называем `m2db`
1. Заходим в контейнер (любые команды, которые вы хотите выполнить с магентой нужно запускать внутри контейнера) 
    `docker exec -it nginx-php-mysql-magento_php74-ap74_1 bash`
1. Теперь мы в докер системе. Нам нужно перейти в папку с магентой
    `cd magento.loc`
1. И запустить команду на установку магенты
    ```
    php bin/magento setup:install --base-url=http://magento.loc/ --db-host=mysql --db-name=m2db --db-user=root --db-password=secret --backend-frontname=admin --admin-firstname=admin --admin-lastname=admin --admin-email=admin@example.com --admin-user=admin --admin-password=a111111 --language=en_US --currency=USD --timezone=America/Chicago --cleanup-database --sales-order-increment-prefix="ORD$" --session-save=db --use-rewrites=1 --search-engine=elasticsearch7 --elasticsearch-host=es7.4 --elasticsearch-port=9200
    ```
1. Магента установлена, но сделаем ещё пару вещей, чтобы нам было удобней работать:
    1. Отключаем двухфакторную аутентификация в админке  
        `php bin/magento mod:disable Magento_TwoFactorAuth`
    1. Переводим магенту в режим разработки
        `php bin/magento dep:mod:set developer`
1. Поздравляю с установкой!  
    Фронт теперь доступен по адресу http://magento.loc/  
    Админка http://magento.loc/admin  
    Username: admin
    Password: a111111

### Дальнейшая работа с контейнерами
При перезагрузке системы контейнеры останавливаются. Поэтому перед работой их нужно заново запустить.  
Для этого выполняем скрипт `./start`  
Чтобы остановить контейнеры- `./stop`  
Чтобы перезапустить- `./restart`  

### Как запускать команды для Magento 2
1. Заходим в контейнер через терминал  
    `docker exec -it nginx-php-mysql-magento_php74-ap74_1 bash`
1. Теперь мы в докер системе. Нам нужно перейти в папку с магентой  
    `cd magento.loc`
1. Выполняем нужные команды `php bin/magento cache:flush`

### Как получить доступ в бд
1. Переходим в браузере по адресу **0.0.0.0:2200**
1. Логинимся как `root`, пароль `secret`


### Настраиваем дебаггер для PhpStorm.

P/S исходники брались отсюда: https://habr.com/ru/post/473184/
