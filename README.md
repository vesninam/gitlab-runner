## Гайд по настройке и использованию GitLab Runner 

### Установите Docker

#### Добавление apt-репозитория для Docker 

```
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```

#### Сама установка Docker 

```
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

#### Постустановочная настройка 

```
sudo groupadd docker
sudo usermod -aG docker $USER
newgrp docker
```

#### Проверка установки докера 

Введите команду: ```docker run hello-world```

Ожидаемый результат: 

![result](https://i.ibb.co/PQ0NW6g/ksnip-20240219-135142.png)

### Установка GitLab Runner 

#### Загрузка и установка раннера 

***Добавьте apt-репозиторий для GitLab Runner***

```curl -L "https://packages.gitlab.com/install/repositories/runner/gitlab-runner/script.deb.sh" | sudo bash```


Установите gitlab-runner командой 

```sudo dpkg -i gitlab-runner_amd64.deb```

#### Настройка раннера 

Для большего удобства работы с проектами, сделаем раннер групповым. Создадим группу 

![group creation](https://i.ibb.co/QXtrwL7/ksnip-20240219-151050.png)

Теперь перейдём в Build -> Runners 

![runners section](https://i.ibb.co/J2cNbzh/ksnip-20240219-151055.png)


Нажимаем Create Group Runner 

![create runner button](https://i.ibb.co/pyfK9Vf/ksnip-20240219-151059.png)


Вносим необходимую информацию: операционную систему, тег (или теги), по которому мы будем отличать данный раннер, описание и максимальное время выполнение работы. 

![info os](https://i.ibb.co/ZNfyYbN/ksnip-20240219-151107.png)

![info another](https://i.ibb.co/r6br1sR/ksnip-20240219-151110.png)


Раннер создан. Теперь его нужно зарегистрировать. Переходим к терминалу и вводим скопированный код.

![codeinfo](https://i.ibb.co/ngcBWW2/ksnip-20240219-151114.png)

1) Платформу и имя раннера оставляем как есть. 
2) В качестве платформы для выполнения выбираем ```docker``` 
3) Образ выбираем по вашему смотрению В качестве примера будет использован ubuntu 

![runner registered](https://i.ibb.co/xfZShc6/ksnip-20240219-151125.png)


Теперь необходимо добавить автоматически созданного пользователя gitlab-runner в группу docker. Для этого воспользуемся командой 

```sudo usermod -aG docker gitlab-runner```

После этого необходимо добавить gitlab-runner в sudoers.
Для этого необходимо прописать выполнить следующее  

```sudo nano  /etc/sudoers```

![alt text](https://i.ibb.co/rtfgRdG/ksnip-20240219-222659.png)


После этого открываем файл ```~/.gitlab-runner/config.toml``` и указываем путь к ```docker.sock``` в ```volumes```

![img](https://i.ibb.co/S7jbDmx/ksnip-20240219-182128.png)


Создадим новый screen, введём команду  ```gitlab-runner run``` и деаттачимся от него (скрина)


После этого должен появиться в списке раннеров.


![isee](https://i.ibb.co/hyDhHjS/ksnip-20240219-151129.png) 



### Использование раннера 

#### Тестовый проект. 

Создадим пустой проект в группе, к которой привязан раннер. В нем созданим файл ```.gitlab-ci.yml``` (сделать это можно либо вручную, либо нажатием соответствующей кнопки )

![ci creation](https://i.ibb.co/x8p2q5y/ksnip-20240219-151133.png)

Напишем внутри следующее: 

```
stages:
    - demo
.demo:
    stage: demo
    tags:
        - demo-runner-usage 
    image: "maven:3.8.3-openjdk-17"

demo:
    extends: .demo
    script:
        - echo "Hello World!"
```

Этот скрипт создает одну единственную стадию demo, во время которой просто выведется строка "Hello World"  

В .demo мы указали такие параметры как 

1) Название стадии, к которой будет применен код .demo
2) Тег раннера, который мы используем 
3) Образ Docker, который будет использоваться во время запуска. В качестве примера был взят образ maven:3.8.3-openjdk-17 (если не указывать, то будет применен стандартный) 

demo - сама стадия, использующая настройки из .demo и выводящая "Hello World" 

Если всё было настроено верно, то pipeline начнет выполнятся


![started](https://i.ibb.co/R63S6br/ksnip-20240219-151141.png)

![started1](https://i.ibb.co/wdqyRgJ/ksnip-20240219-151144.png)

Если откроем pipeline, то увидим следующее  

![started2](https://i.ibb.co/yXJvKmL/ksnip-20240219-151206.png)

#### Использование GitLab CI и Docker Compose для поднятия рабочего окружения 

При помощи GitLab CI и Docker Compose на сервере можно поднять всю необходимое для работы проекта инфраструктуру.  В качестве примера рассмотрим такие сервисы как PostgreSQL и Dozzle.


Создадим новый проект, назовём его ```project-infra```

По аналогии с предыдущим пунктом, создадим файл ```.gitlab-ci.yml```

И напишем в нем следуюее 

```
stages:
  - deploy-infra

variables:
  HOST_SOURCES: /opt/sources

deploy-infra:
  image:  "docker/compose:1.25.1"
  stage: deploy-infra
  script:
    # останавливаем контейнеры
    - docker-compose stop

    # удаляем старую копию файлов и копируем новую
    - rm -rf $HOST_SOURCES/*
    - cp -r $CI_PROJECT_DIR/* $HOST_SOURCES

    # запускаем контейнеры
    - docker-compose up -d

  when: manual # Запуск действия проводится вручную
  tags:
    - demo-runner-usage
```
В данном .gitlab-ci.yml описывается основное поведение: мы останавливаем контейнеры, отвечающие за инфраструктуру, очищаем и перезаписываем файлы, потом при помощи команды ```docker-compose up -d``` поднимаем нужные сервисы. Сервисы описываются в файле ```.docker-compose.yml``` Его содержимое для данного проекта приведено ниже 

```
version: '2.4'
services:
  postgres:
    container_name: postgres
    image: 'postgres:latest'
    environment:
      POSTGRES_USER: db-user
      POSTGRES_PASSWORD: db-password
    ports:
      - '5432:5432'
    volumes:
      - 'db-data:/var/lib/postgresql/data'
    networks:
      - projects-network
  dozzle:
    container_name: dozzle
    image: 'amir20/dozzle:latest'
    ports:
      - '8888:8080' # Проброс портов
    networks:
      - projects-network
networks:
  projects-network:
    driver: bridge
    external: true

volumes:
  db-data: null
```
В docker-compose.yml описываются сервисы, названия их docker-образов, проводится проброс портов, указываются переменные окружения. Также можно заметить указание network - сети, которую используют контейнеры. Чтобы контейнеры "видели" друг друга, они должны находиться в рамках одной сети. В нашем случае, это ```projects-network```

Если всё было настроено правильно, в pipeline будет выведено следующее 

![alt text](https://i.ibb.co/nkKkRZY/ksnip-20240219-182109.png)

А при вводе команды ```docker container ls``` на стороне сервера, мы заметим новые контейнеры. 

![alt img](https://i.ibb.co/Zf28vFQ/ksnip-20240219-182155.png)

#### Использование GitLab Runner для деплоя, а также запуска Unit- и E2E-тестов 

##### Запуск Unit-тестов при помощи GitLab Runner (PyTest)

Создадим подгруппу под наш проект, назовём её ```super-backend-app``` . В ней создадим два проекта ```backend-app``` и ```e2e-tests```

![alt text](https://i.ibb.co/7VRRjZ7/ksnip-20240222-102118.png)

Сделаем для backend-app простенькое приложение на Python с использованием фреймворка FastAPI. Там будет всего два файла: ```server.py``` со следующим содержимим 

```
from fastapi import FastAPI

app = FastAPI()


@app.get("/")
async def root():
    return {"message": "Hello World"}

```

И ```test_main.py``` в директории test. Содержимое файла представлено ниже 

```
def test_first():
    assert 2 + 2 == 4


def test_second():
    assert "A" != "B"

```

***Не забываем про файл*** ```requirements.txt``` !

Создадим файл ```.gitlab-ci.yml```

И напишем в нем следующее 

```
stages:
    - test

run-tests:
    image: python:3.9
    stage: test
    tags:
        - demo-runner-usage 
    before_script:
        - pip install -r requirements.txt
    script:
        - pytest .

```

В yml файле мы указали образ докера который будет использоваться (в нашем случае это python 3.9), тег используемого раннера, команды, которые надо выполнить перед основным скриптом (у нас это установка зависимостей из requirements.txt), а также команду на запуск тестов в основном скрипте. 






