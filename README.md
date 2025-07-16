Зарубов Николай
Домашнее задание к занятию «Система мониторинга Zabbix»

Задание 1
Установите Zabbix Server с веб-интерфейсом.

Процесс выполнения
Выполняя ДЗ, сверяйтесь с процессом, описанным в записи лекции.
Установите PostgreSQL. Для установки достаточно той версии, которая есть в системном репозитории Debian 11.
Используя конфигуратор команд с официального сайта, составьте набор команд для установки последней версии Zabbix с поддержкой PostgreSQL и Apache.
Выполните все необходимые команды для установки Zabbix Server и Zabbix Web Server.
Требования к результатам
Прикрепите к файлу README.md скриншот авторизации в админке.
Приложите к файлу README.md текст использованных команд на GitHub.

Решение:

Установить PostgreSQL:
sudo apt install postgresq

 Установить репозиторий Zabbix

# wget https://repo.zabbix.com/zabbix/6.0/debian/pool/main/z/zabbix-release/zabbix-release_latest_6.0+debian11_all.deb
# dpkg -i zabbix-release_latest_6.0+debian11_all.deb
# apt update
б) Установить сервер Zabbix, frontend, агент
# apt install zabbix-server-pgsql zabbix-frontend-php php7.4-pgsql zabbix-apache-conf zabbix-sql-scripts zabbix-agent

Создать начальную базу данных

sudo -u postgres createuser --pwprompt zabbix
sudo -u postgres createdb -O zabbix zabbix 

Создание пользователя с помощью psql из-под root

su - postgres -c 'psql --command "CREATE USER zabbix WITH PASSWORD
'\'123456789\'';"'

su - postgres -c 'psql --command "CREATE DATABASE zabbix OWNER zabbix;"

Импортируем схему
zcat /usr/share/zabbix-sql-scripts/postgresql/server.sql.gz | sudo -u zabbix psql zabbix 

Задаём пароль в DBPassword

sed -i 's/# DBPassword=/DBPassword=123456789/g' /etc/zabbix/zabbix_server.conf

Запускаем Zabbix server, Zabbix agent и веб-сервер

systemctl restart zabbix-server zabbix-agent apache2
systemctl enable zabbix-server zabbix-agent apache2




Задание 2

Установите Zabbix Agent на два хоста.

Процесс выполнения
Выполняя ДЗ, сверяйтесь с процессом, описанным в записи лекции.
Установите Zabbix Agent на 2 виртуальных машины, одна из которых может быть вашим Zabbix-сервером.
Добавьте Zabbix Server в список разрешённых серверов для ваших Zabbix-агентов.
Добавьте Zabbix Agentов в раздел Configuration > Hosts вашего Zabbix Servera.
Убедитесь, что в разделе «Последние данные» начали появляться сведения от добавленных агентов.
Требования к результатам
Приложите в файл README.md скриншот раздела Configuration > Hosts, где видно, что агенты подключены к серверу
Приложите к файлу README.md скриншот лога агента Zabbix, на котором видно, что он работает с сервером
Приложите в файл README.md скриншот раздела Monitoring > Latest data для обоих хостов, где видны поступающие от агентов данные.
Приложите к файлу README.md текст использованных команд на GitHub

Решение:

Установка Zabbix agent

Добавить репозиторий Zabbix

wget https://repo.zabbix.com/zabbix/6.0/debian/pool/main/z/zabbix-release/zabbix-release_latest_6.0+debian11_all.deb
dpkg -i zabbix-release_latest_6.0+debian11_all.deb
apt update 

Установить Zabbix agent и компоненты

apt install zabbix-agent

Запустить Zabbix agen

systemctl restart zabbix-agent
systemctl enable zabbix-agent 

Настройка Zabbix agent 
на удалённом хосте

Перенастроить агент так, чтобы он принял подключение от сервера:

sudo nano /etc/zabbix/zabbix_agentd.conf

В раздел Server добавьте адрес сервера или просто заменить имеющийся

Перезапустить агента:

sudo systemctl restart zabbix-agent

Для автоматизации с помощью bash-скриптов можно использовать команду:

sed -i 's/Server=127.0.0.1/Server=10.129.0.7/' /etc/zabbix/zabbix_agentd.conf

Меняем адрес сервера в zabbix_agentd.conf на свой

Теперь сервер сможет подключиться к агенту







