# Домашнее задание к занятию 5 «Тестирование roles»

## Подготовка к выполнению

1. Установите molecule и его драйвера: `pip3 install "molecule molecule_docker molecule_podman`.
2. Выполните `docker pull aragast/netology:latest` —  это образ с podman, tox и несколькими пайтонами (3.7 и 3.9) внутри.

## Основная часть

Ваша цель — настроить тестирование ваших ролей. 

Задача — сделать сценарии тестирования для vector. 

Ожидаемый результат — все сценарии успешно проходят тестирование ролей.

### Molecule

1. Запустите  `molecule test -s ubuntu_xenial` (или с любым другим сценарием, не имеет значения) внутри корневой директории clickhouse-role, посмотрите на вывод команды.
Данная команда может отработать с ошибками или не отработать вовсе, это нормально. Наша цель - посмотреть как другие в реальном мире используют молекулу И из чего может состоять сценарий тестирования.
```root@test:~/IdeaProjects/08-ansible-05-testing/tem-test/ansible-clickhouse# 
root@test:~/IdeaProjects/08-ansible-05-testing/tem-test/ansible-clickhouse# molecule list
WARNING  Driver docker does not provide a schema.
CRITICAL Failed to validate /root/IdeaProjects/08-ansible-05-testing/tem-test/ansible-clickhouse/molecule/debian_bullseye/molecule.yml

["Additional properties are not allowed ('playbooks' was unexpected)"] 
```
- Molecule говорит, что в файле molecule/debian_bullseye/molecule.yml есть неправильный ключ playbooks. Это означает, что сценарий написан для старой версии Molecule, а установлена новая, где синтаксис изменился.
2. Перейдите в каталог с ролью vector-role и создайте сценарий тестирования по умолчанию при помощи `molecule init scenario --driver-name docker`.
3. Добавьте несколько разных дистрибутивов (oraclelinux:8, ubuntu:latest) для инстансов и протестируйте роль, исправьте найденные ошибки, если они есть.
4. Добавьте несколько assert в verify.yml-файл для  проверки работоспособности vector-role (проверка, что конфиг валидный, проверка успешности запуска и др.). 
5. Запустите тестирование роли повторно и проверьте, что оно прошло успешно.
6. Добавьте новый тег на коммит с рабочим сценарием в соответствии с семантическим версионированием.

### Tox

1. Добавьте в директорию с vector-role файлы из [директории](./example).
2. Запустите `docker run --privileged=True -v <path_to_repo>:/opt/vector-role -w /opt/vector-role -it aragast/netology:latest /bin/bash`, где path_to_repo — путь до корня репозитория с vector-role на вашей файловой системе. 
3. Внутри контейнера выполните команду `tox`, посмотрите на вывод.
-Падает с ошибкой 'molecule/compatibility/molecule.yml' glob failed — так как сценария compatibility нет
5. Создайте облегчённый сценарий для `molecule` с драйвером `molecule_podman`. Проверьте его на исполнимость.
- source .tox/py39-ansible210/bin/activate
- molecule init scenario podman --driver-name podman
- ``` python version = 3.9.2 (default, Jun 13 2022, 19:42:33) [GCC 8.5.0 20210514 (Red Hat 8.5.0-10)]


PLAY [Create a new molecule scenario] ******************************************

TASK [Check if destination folder exists] **************************************
changed: [localhost]

TASK [Check if destination folder is empty] ************************************
ok: [localhost]

TASK [Fail if destination folder is not empty] *********************************
skipping: [localhost]

TASK [Expand templates] ********************************************************
changed: [localhost] => (item=molecule/podman/create.yml)
changed: [localhost] => (item=molecule/podman/molecule.yml)
changed: [localhost] => (item=molecule/podman/destroy.yml)
changed: [localhost] => (item=molecule/podman/converge.yml)

PLAY RECAP *********************************************************************
localhost                  : ok=3    changed=2    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0

INFO     Initialized scenario in /opt/vector-role/molecule/podman successfully.
(py39-ansible210) [root@1d205c672e5b vector-role]#  ```
6. Пропишите правильную команду в `tox.ini`, чтобы запускался облегчённый сценарий.
``` [tox]
minversion = 1.8
envlist = py37-ansible{210,30}
skipsdist = true 
```

7. Запустите команду `tox`. Убедитесь, что всё отработало успешно.
``` 
(py39-ansible210) [root@1d205c672e5b vector-role]# tox                                                                                                                                                                                                                                                              
py37-ansible210 recreate: /opt/vector-role/.tox/py37-ansible210
py37-ansible210 run-test-pre: PYTHONHASHSEED='1845711659'
py37-ansible30 recreate: /opt/vector-role/.tox/py37-ansible30
py37-ansible30 run-test-pre: PYTHONHASHSEED='1845711659'
_____________________________________________________________________________________________________________________________________________________ summary ______________________________________________________________________________________________________________________________________________________
  py37-ansible210: commands succeeded
  py37-ansible30: commands succeeded                                                                                                                                                                                                                                                                                
  congratulations :)                                                                                                                                                                                                                                                                                                
(py39-ansible210) [root@1d205c672e5b vector-role]#        

```
8. Добавьте новый тег на коммит с рабочим сценарием в соответствии с семантическим версионированием.

После выполнения у вас должно получится два сценария molecule и один tox.ini файл в репозитории. Не забудьте указать в ответе теги решений Tox и Molecule заданий. В качестве решения пришлите ссылку на  ваш репозиторий и скриншоты этапов выполнения задания. 

## Необязательная часть

1. Проделайте схожие манипуляции для создания роли LightHouse.
2. Создайте сценарий внутри любой из своих ролей, который умеет поднимать весь стек при помощи всех ролей.
3. Убедитесь в работоспособности своего стека. Создайте отдельный verify.yml, который будет проверять работоспособность интеграции всех инструментов между ними.
4. Выложите свои roles в репозитории.

В качестве решения пришлите ссылки и скриншоты этапов выполнения задания.

---

### Как оформить решение задания

Выполненное домашнее задание пришлите в виде ссылки на .md-файл в вашем репозитории.
