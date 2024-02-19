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
