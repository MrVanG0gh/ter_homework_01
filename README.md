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
Согласно логике файла всю персональную информацию можно хранить в файле `personal.auto.tfvars` (файл не будет находиться под версионным контролем)

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

![Screen_3_2](https://github.com/MrVanG0gh/ter_homework_01/blob/main/Screenshots/Screenshot_3_2.png)

`"result":
"opOUco3zp2j8jrtQ",`


