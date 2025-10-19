# Домашнее задание к занятию «Введение в Terraform» - `Иншаков Владимир`

### Чек-лист готовности к домашнему заданию

1. Скачайте и установите **Terraform** версии >=1.8.4 . Приложите скриншот вывода команды ```terraform --version```.

Из облака Yandex был установлен дистрибутив Terraform версии 1.9.8:
![Screen_0_1](https://github.com/MrVanG0gh/ter_homework_01/blob/main/Screenshots/Screenshot_0_1.png)

2. Скачайте на свой ПК этот git-репозиторий. Исходный код для выполнения задания расположен в директории **01/src**.

Была добавлена директория `src` из репозитория с заданием:
![Screen_0_2](https://github.com/MrVanG0gh/ter_homework_01/blob/main/Screenshots/Screenshot_0_2.png)

3. Убедитесь, что в вашей ОС установлен docker.

На виртуальной системе установлен Docker версии 28.4.0:
![Screen_0_3](https://github.com/MrVanG0gh/ter_homework_01/blob/main/Screenshots/Screenshot_0_3.png)


### Задание

1. Перейдите в каталог [**src**](https://github.com/netology-code/ter-homeworks/tree/main/01/src). Скачайте все необходимые зависимости, использованные в проекте. 
2. Изучите файл **.gitignore**. В каком terraform-файле, согласно этому .gitignore, допустимо сохранить личную, секретную информацию?(логины,пароли,ключи,токены итд)
3. Выполните код проекта. Найдите  в state-файле секретное содержимое созданного ресурса **random_password**, пришлите в качестве ответа конкретный ключ и его значение.
4. Раскомментируйте блок кода, примерно расположенный на строчках 29–42 файла **main.tf**.
Выполните команду ```terraform validate```. Объясните, в чём заключаются намеренно допущенные ошибки. Исправьте их.
5. Выполните код. В качестве ответа приложите: исправленный фрагмент кода и вывод команды ```docker ps```.
6. Замените имя docker-контейнера в блоке кода на ```hello_world```. Не перепутайте имя контейнера и имя образа. Мы всё ещё продолжаем использовать name = "nginx:latest". Выполните команду ```terraform apply -auto-approve```.
Объясните своими словами, в чём может быть опасность применения ключа  ```-auto-approve```. Догадайтесь или нагуглите зачем может пригодиться данный ключ? В качестве ответа дополнительно приложите вывод команды ```docker ps```.
7. Уничтожьте созданные ресурсы с помощью **terraform**. Убедитесь, что все ресурсы удалены. Приложите содержимое файла **terraform.tfstate**. 
8. Объясните, почему при этом не был удалён docker-образ **nginx:latest**. Ответ **ОБЯЗАТЕЛЬНО НАЙДИТЕ В ПРЕДОСТАВЛЕННОМ КОДЕ**, а затем **ОБЯЗАТЕЛЬНО ПОДКРЕПИТЕ** строчкой из документации [**terraform провайдера docker**](https://docs.comcloud.xyz/providers/kreuzwerker/docker/latest/docs).  (ищите в классификаторе resource docker_image )

---

### Решение

#### 1

Для того, чтобы скачать все необходимые зависимости для проекта используем команду `terraform init`

![Screen_1](https://github.com/MrVanG0gh/ter_homework_01/blob/main/Screenshots/Screenshot_1.png)

---

#### 2
Содержимое файла `.gitignore`:
```
# Local .terraform directories and files
**/.terraform/*
.terraform*

!.terraformrc

# .tfstate files
*.tfstate
*.tfstate.*

# own secret vars store.
personal.auto.tfvars
```
Согласно логике автора всю персональную информацию можно хранить в файле `personal.auto.tfvars` (он не будет находиться под версионным контролем)

---
#### 3

Выполним код проекта при помощи команды `terraform apply`

![Screen_3_1](https://github.com/MrVanG0gh/ter_homework_01/blob/main/Screenshots/Screenshot_3_1.png)

В state-файле найдем значение секретного содержимого созданного ресурса `random_password`:

```
{
  "version": 4,
  "terraform_version": "1.9.8",
  "serial": 1,
  "lineage": "28ab1746-24e6-7e9e-210d-f62c1071d31b",
  "outputs": {},
  "resources": [
    {
      "mode": "managed",
      "type": "random_password",
      "name": "random_string",
      "provider": "provider[\"registry.terraform.io/hashicorp/random\"]",
      "instances": [
        {
          "schema_version": 3,
          "attributes": {
            "bcrypt_hash": "$2a$10$cvsyIGnIJq15oAyO4aPbQ.DBuezF/6Mjn6lZbLFWgPNv9DXYLaIOu",
            "id": "none",
            "keepers": null,
            "length": 16,
            "lower": true,
            "min_lower": 1,
            "min_numeric": 1,
            "min_special": 0,
            "min_upper": 1,
            "number": true,
            "numeric": true,
            "override_special": null,
            "result": "opOUco3zp2j8jrtQ",
            "special": false,
            "upper": true
          },
          "sensitive_attributes": [
            [
              {
                "type": "get_attr",
                "value": "bcrypt_hash"
              }
            ],
            [
              {
                "type": "get_attr",
                "value": "result"
              }
            ]
          ]
        }
      ]


```
Искомое значение:

`"result":
"opOUco3zp2j8jrtQ",`

---

#### 4

Раздокументированный блок кода выглядит следующим образом:
```

resource "docker_image" { # ошибка1
  name         = "nginx:latest"
  keep_locally = true
}

resource "docker_container" "nginx" { # ошибка2
  image = d_image.nginx.image_id
  name  = "example_${random_password.random_string_FAKE.resulT}" # ошибка3

  ports {
    internal = 80
    external = 9090
  }
}
```
Ошибка1:
Согласно документации объявление `resource` должно быть следующим:

`resource "<TYPE>" "<LABEL>"   block`
 В нашем случае, тип ресурса задан, но не хватает имени. Исправим на:

`resource "docker_image" "nginx'  {`
На это имя будет ссылка далее.

Ошибка2:
Согласно документации имя для объекта не должно начинаться с цифры. Поэтому уберем лишнее и получим:

`resource "docker_container" "nginx" {`

Ошибка3:
`name  = "example_${random_password.random_string_FAKE.resulT}" # ошибка3`

Здесь допущена опечатка в атрибутах ресурса random_password. После исправлений все выглядит следующим образом:

`name  = "example_${random_password.random_string.result}"`

Исправленная часть кода будет выглядеть следующим образом:

```
resource "docker_image" "nginx" {
  name         = "nginx:latest"
  keep_locally = true
}

resource "docker_container" "nginx" {
  image = docker_image.nginx.image_id
  name  = "example_${random_password.random_string.result}"

  ports {
    internal = 80
    external = 9090
  }
}
```

---

#### 5

Выполним код после исправления ошибок и опечаток:
![Screen_5_1](https://github.com/MrVanG0gh/ter_homework_01/blob/main/Screenshots/Screenshot_5_1.png)

Видим, что имя контейнера содержит сгенерированную нами строку после example_:
```
van@vans-srv:~/git/ter_homework_01/src$ docker ps
CONTAINER ID   IMAGE          COMMAND                  CREATED          STATUS          PORTS                  NAMES
881b00185a81   e35ad067421c   "/docker-entrypoint.…"   18 seconds ago   Up 17 seconds   0.0.0.0:9090->80/tcp   example_opOUco3zp2j8jrtQ

```
---

#### 6

Задокументируем строку с именем контейнера и заменим ее на `hello_world`
```
resource "docker_container" "nginx" {
  image = docker_image.nginx.image_id
  # name  = "example_${random_password.random_string.result}"
  name = "hello_world"

  ports {
    internal = 80
    external = 9090
  }
}
```

![Screen_6_1](https://github.com/MrVanG0gh/ter_homework_01/blob/main/Screenshots/Screenshot_6_1.png)

Применение ключа -auto-approve опасно тем, что подтверждение изменений происходит без подтверждения со стороны человека. Возможны неожиданные результаты и негативные изменения в инфраструктуре.
Если же есть полная уверенность в правильности конфигурации, то использование данного ключа может позволить применять скрипты для автоматического разворачивания инфраструктуры.

#### 7

Уничтожаем созданные ресурсы при помощи команды `terraform destroy`

Файл terraform.tfstate содержит в себе:
```
{
  "version": 4,
  "terraform_version": "1.9.8",
  "serial": 11,
  "lineage": "28ab1746-24e6-7e9e-210d-f62c1071d31b",
  "outputs": {},
  "resources": [],
  "check_results": null
}
```

#### 8

Изучив документацию, отметил, что ключ `keep_locally` определяет должен ли храниться docker image после скачивания, или нет.

```
keep_locally (Boolean) If true, then the Docker image won't be deleted on destroy operation. If this is false, it will delete the image from the docker local storage on destroy operation.
```

В коде было:

```
resource "docker_image" "nginx" {
  name         = "nginx:latest"
  keep_locally = true
}
```
Значит, docker image не будет удален после применения команды `destroy`.