# SEGA_>DockerRepository

1. Клонируем репозиторий на гитхабе. Переименовываем .env-example в .env файл с переменными.
USER_ID=ваш uid
GROUP_ID=ваш gid

2. Прописываем узел ap74.magento.loc в файле /etc/hosts

127.0.0.1 ap74.magento.loc

3. В папке с гитом запускаем команды:
chmod +x ./stop && chmod +x ./start && chmod +x ./restart && ./start

4. Настраиваем проект ap74.magento.loc в PhpStorm.

P/S исходники брались отсюда: https://habr.com/ru/post/473184/

Тут и настройка PhpStorm под Xdebug

Скачиваем и устанавливаем магенту:

https://www.dropbox.com/s/nn1r6ybtncdbhpc/magento-ce-2.4.1_sample_data-2020-09-25-04-46-06.zip?dl=0

php bin/magento setup:install --base-url=http://ap74.magento.loc/ --db-host=mysql --db-name=m2db --db-user=root --db-password=secret --admin-firstname=admin --admin-lastname=admin --admin-email=admin@example.com --admin-user=admin --admin-password=a111111 --language=en_US --currency=USD --timezone=America/Chicago --cleanup-database --sales-order-increment-prefix="ORD$" --session-save=db --use-rewrites=1 --search-engine=elasticsearch7 --elasticsearch-host=es7.4 --elasticsearch-port=9200