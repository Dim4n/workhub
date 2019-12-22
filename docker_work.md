## Что такое Докер

https://guides.hexlet.io/docker/

## Образы и контейнеры

В Docker есть две важные концепции: образы (images) и контейнеры (containers).

    Image: список инструкций для всех программных пакетов в ваших проектах
    Container: экземпляр образа во время выполнения

Другими словами, образ (image) описывает, что произойдет, а контейнер (container) – это то, что фактически выполняется.
Для настройки образов и контейнеров в Docker мы используем два файла: Dockerfile и docker-compose.yml.
Dockerfile содержит список инструкций для образа, иначе говоря, что на самом деле происходит в среде контейнера.
Создадим новый файл Dockerfile.

### Быстрый обзор

Вот краткая версия терминов и понятий, которые мы рассмотрели в этом посте:

    Image: «определение» вашего проекта
    Container: это то что ваш проект на самом деле работает (экземпляр образа)
    Dockerfile: определяет, как выглядит ваш образ
    docker-compose.yml: YAML-файл, который задает как должны работать Docker образы

Мы используем Dockerfile, чтобы сообщить Docker, как создать наш образ. Затем мы запускаем наш реальный проект в контейнере. Файл docker-compose.yml предоставляет дополнительную информацию о том, как наш контейнер Docker должен вести себя.


## Настройка на Ubuntu

```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
```
если ошибка: error add-apt-repository command not found
```
sudo apt install software-properties-common
```

```
sudo apt-get update
apt-cache policy docker-ce
sudo apt-get install -y docker-ce
sudo systemctl status docker
```

Для того чтобы добавить можно было запускать докер-команды от обычного пользователя:

```
sudo usermod -aG docker ${USER}
su - ${USER}
id -nG
```

Подробнее: https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-16-04

## Установка docker-compose

```
sudo curl -L https://github.com/docker/compose/releases/download/1.18.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
docker-compose --version
```

Подробнее: https://www.digitalocean.com/community/tutorials/how-to-install-docker-compose-on-ubuntu-16-04

## Работа

### Основные команды

```
docker-compose ps
docker-compose run service bash

после того как добавил зависимость в requirements
docker-compose exec service bash

#запускаем в отдельном процессе
docker-compose up -d

#установка зависимостей pip  - когда добавил в Dockerfiles зависимость
docker-compose up -d --build

#логи
docker-compose logs service
docker-compose logs -f --tail=100 service

#перезагрузка сервера
docker-compose restart service

#запускаю сервер
docker-compose exec run service

#запускаю bash
docker-compose exec service bash

#запущенные контейнеры
docker-compose ps

#если Container not Found а нужно запустить npm или yarn
docker-compose run frontend_service bash
yarn install
```

```
docker-compose exec postgres_service bash
psql -U postgres
```

```
Все команды выполняются в корневой директории (project, там где лежит файл docker-compose.yml)
Запуск (первый раз) все сервисы: docker-compose up -d
Запуск (после остановки) все сервисы: docker-compose start
Остановить все сервисы: docker-compose stop
Остановить один конкретный сервис: docker-compose stop <имя-сервиса>
Запустить один конкретный сервис: docker-compose start <имя-сервиса>
<имя сервиса> - это одно из: server|admin|db|client (все это можно найти в docker-compose.yml файле).
```

#### Если поменялось имя пользователя в postgres в docker-compose.yml

```
#удалить volume
docker volume ls
docker volume rm service_db-data
docker-compose up -d # создаст новый volume
```

#### js front end
```
npm install
# если нет прав - проверить ls -la, сравнить c id -u и если разные значения, в docker-compose прописать:
build:
  args:
    - UID=то число, что выдало ls -la
    - GID=тоже самое число (группа)
```
    
#### docker-compose.override.yml

Позволяет переопределить настройки в docker-compose.yml
Полезно, если на тестовом и на проде разные настройки

#### после выкатки изменений на фронтенд в прод
```
docker-compose exec frontend bash
yarn run build # установить зависимости на проде
```

```
# если ошибки при --build
docker-compose run frontend yarn
# потом попробовать
docker-compose up -d --build
```

```
# если всё время Exit напротив frontend, попробовать:
docker-compose run frontend bash
yarn
```

#### Cкопировать файл из контейнера

```
docker cp db_service:/db_dump.dump ~/db_dump.dump
```
