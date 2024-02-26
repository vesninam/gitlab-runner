## GitLab Runner. 

![alt text](https://i.ibb.co/MN0k8CS/jtwqwgm4eemxyqz8lcd3.jpg)

### Что такое GitLab Runner?

GitLab Runner - это компонент GitLab, который обеспечивает автоматизацию процесса сборки, тестирования и развертывания программного кода. Этот инструмент позволяет запускать задачи CI/CD (Continuous Integration/Continuous Deployment) на удаленных машинах или в контейнерах.


### Какие возможности он предоставляет? 

![alt text](https://i.ibb.co/zRtfyfC/1-0-Ltf-O6-HTqdy-ZDX7p-YPu-WA.webp)

1) ***Управление тестированием:***
      - Запуск тестов одним кликом, включая как unit, так и e2e тестирование.
      - Генерация подробных отчетов о тестировании, поддержка форматов Allure или htmlcov.

2) ***Сборка и развертывание приложения***:
      - Выполнение сборки приложения на сервере с получением результатов.
      - Деплой приложения на сервер с возможностью автоматического развертывания в различные окружения (например, staging или production).

3) ***Контроль качества кода***:
      - Настройка блокировки merge или деплоя до прохождения сборки и тестирования.
      - Отсутствие сборки или неуспешные тесты препятствуют выполнению заданных действий.

4) ***Статический анализ кода***:
      - Запуск тестов статическими кодоанализаторами, например, flake8.

5) ***Автоматизированный деплой в различные окружения***:
      - Конфигурирование автоматического деплоя приложения в различные окружения, обеспечивая эффективный процесс развертывания.

6) ***Поддержка множественных платформ***:
      - Поддержка различных операционных систем и платформ для тестирования и разработки приложения.

7) ***Интеграция с CI/CD инструментами***:
      - Интеграция с различными инструментами CI/CD для настройки и автоматизации непрерывной интеграции и доставки.

8) ***Параллельное выполнение задач***:
      - Возможность параллельного выполнения задач, ускоряя процесс сборки и тестирования.

9) ***Использование Docker контейнеров***:
      - Интеграция с Docker для создания и использования контейнеров, обеспечивая изоляцию среды выполнения задач и консистентность процессов.

10) ***Легкая масштабируемость***:
      - Легкая добавляемость дополнительных экземпляров Runner'ов для обработки больших объемов задач и эффективного распределения нагрузки.

11) Много другое


### Что не умеет GitLab Runner? 


1) ***Управление базами данных***: GitLab CI/CD может помочь в создании и развертывании баз данных, но не предоставляет полного управления жизненным циклом баз данных. Управление миграциями, резервными копиями и масштабированием баз данных может потребовать использования дополнительных инструментов.

2) ***Управление конфиденциальной информацией***: GitLab CI/CD предоставляет механизмы для хранения переменных, но вы должны быть осторожны при управлении конфиденциальной информацией, такой как секреты API или ключи. Использование инструментов управления секретами в сочетании с GitLab CI/CD может быть рекомендовано для обеспечения безопасности.



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


Теперь создадим сеть для Docker (разрабатываемые микросервисы будут находиться в этой сети)

```
 docker network create -d bridge projects-network
```


После этого открываем файл ```~/.gitlab-runner/config.toml``` и указываем путь к ```docker.sock``` в ```volumes```

![img](https://i.ibb.co/S7jbDmx/ksnip-20240219-182128.png)


После этого укажем network, который будет использовать docker executor. Добавим в ```[runners.docker]``` строку ``` network_mode = "projects-network"```

![img](https://i.ibb.co/tp5Tt2X/ksnip-20240226-172521.png)


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
    return {"message": "OK"}

```

И ```test_main.py``` в директории test. Содержимое файла представлено ниже 

```
def test_first():
    assert 2 + 2 == 4


def test_second():
    assert "A" != "B"

```

***Не забываем про файл*** ```requirements.txt``` !

```
fastapi
uvicorn
pytest
```


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

![alt text](https://i.ibb.co/Jj8HH4v/ksnip-20240222-110322.png)


После коммита пайплайн начинает работу. После завершения видим следующее: 

![alt text](https://i.ibb.co/CP60PSC/ksnip-20240222-104425.png)


Внесем изменения. Добавим тест, который будет фейлится.  

```
def test_broken():
  assert 2 + 2 == 5
```

Посмотрим как изменится результат. 

![alt text](https://i.ibb.co/0qR3fw9/ksnip-20240222-105348.png)


Как мы видим, пайплайн провалился из-за непройденного теста.В таком случае ни деплой ни дальнейшее слияние веток ***не является возможным***. 

![alt text](https://i.ibb.co/pvkNKjc/ksnip-20240222-105451.png)



##### Деплой проекта при помощи Gitlab CI  

Тесты прошли, значит можно развёртывать приложение на удаленном сервере. Для деплоя будем использовать настроенный ранее раннер. 

Создадим новый этап в ```.gitlab-ci.yml```, этап ```deploy```

```
deploy:
    stage: deploy
    tags:
        - demo-runner-usage
    image: docker/compose:1.25.1
    script:
        - docker stop backend-app || true
        - docker rm backend-app || true
        - docker-compose stop
        - docker-compose up -d --build  
        - docker container ls
```

Теперь нам нужно описать развертываемый сервис в ```docker-compose.yml```

```
version: "3.7"
services:
  backend-app:
    container_name: backend-app
    networks: 
      - projects-network
    build: 
      context: .
      dockerfile: Dockerfile
    ports:
      - 80:80
    restart: "no"

networks:
  projects-network:
    driver: bridge
    external: true

```

Остановимся на структуре директорий провекта. Сам main файл (как и остальной код) находятся в директории app.  

![alt text](https://transfer.sh/FAkGbe4B9W/ksnip_20240226-155744.png)



После этого нам нужно описать запуск проекта в Docekrfile. Создадим его и укажем следующее 

```
FROM python:3.10.5-alpine #Образ, который мы будем использовать
WORKDIR /code #Название рабочей директории 
COPY ./app/requirements.txt  /code/requirements.txt #Копируем файл requirements.txt во внутрь контейнера
COPY ./app /code/app #Копируем директорию с проектов в контейнер
RUN pip install -r /code/requirements.txt #Установка зависимостей
CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "80"] #Команда для запуска 
```

Запускаем этап deploy. Если всё настроено правильно, то увидим следующий результат: 

![img](https://i.ibb.co/5T5hmTX/ksnip-20240226-160140.png)

Появился новый контейнер ```backend-app```. Теперь проверим, правильно ли произошло развертывание. Отправим GET-запрос на адрес машины. Если всё получилось, увидим следующий результат

![alt text](https://i.ibb.co/v3B2QsL/ksnip-20240226-160549.png)


##### Запуск E2E-Тестов.

Процесс запуска e2e-тесто не имеет больших отличий от запуска unit-тестов. Создадим новый проект, назовем его ```e2e-tests```. В проекте создадим директорию test, там создадим файл ```test_e2e.py```. Содержимое файла приведено ниже: 

```
import requests

def test_e2e_demo():
    url = "http://backend-app:80/"
    result = requests.get(url=url).json()
    assert result is not None
    assert result["message"] == "OK"
```

***Примечание***: вместо IP адреса мы указываем имя контейнера. Так как запуск тестов происходит внутри docker-контейрера, если мы укажем просто адрес, то обращение произойдет внутри контейнера.

Содержимое ```.gitlab-ci.yml``` приведено ниже: 

```
stages:
    - test

test_e2e:
    image: python:3.9
    stage: test
    tags:
        - demo-runner-usage
    before_script:
        - pip install pytest requests
    script:
        - pytest .
    when: manual
```


