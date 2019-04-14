# Ansible simple

## Установка

```
https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html

```
- Для работы ansible на удаленных машинах выполните команду:
```
test -e /usr/bin/python || (apt -y update && apt install -y python-minimal)

```
## Создание конфигурационных файлов

### Файл удаленных машин hosts.txt

- В рабочей директории создайте пустой файл hosts.txt
- Добавьте удаленные машины в файл(для группировки удаленных машин используйте квадратные скобки - [название группы]):
```
[название группы]
<уникальный идентификатор> ansible_host=<адрес машины> ansible_user=<имя пользователя> ansible_ssh_private_key_file=<путь до публичного ssh ключа>
```
- Для проверки работоспособности hosts.txt введите команду:
```
ansible -i hosts.txt all -m ping
```
### Конфигурационный файл ansible.cfg
- В рабочей директории создайте пустой файл ansible.cfg
- Конфигурация:
```
[defaults]
host_key_checking = false
invertory         = ./hosts.txt
```
    - host_key_checking - отключает проверку fingerprint при коннекте
    - inventory - устанавливает файл hosts.txt(после установки в командах не нужно указывать файл)

- Полная информация о всех настройках:
```
https://docs.ansible.com/ansible/2.4/intro_configuration.html
```
## Команды Ad-Hoc (основные) (флаг -b запускает используя sudo):
- Конфигурация удаленной машины:
```
ansible <машина или группа машин или all> -m setup
```
- Выполнение команд на удаленных машинах:
```
ansible <машина или группа машин или all> -m shell -a "<команда>"
```
- Копирование файлов на удаленной машине:
```
ansible <машина или группа машин или all> -m copy -a "src=<путь до файла на локальной машине> dest=<путь на удаленной машине> mode=777" -b
```
- Удаление файлов на удаленной машине:
```
ansible <машина или группа машин или all> -m file -a "path=<путь до файла на удаленной машине> state=absent" -b
```
    Полное описание данного модуля:
    ```
    https://docs.ansible.com/ansible/latest/modules/file_module.html
    ```
- Скачивание файла на удаленную машину:
```
ansible <машина или группа машин или all> -m get_url -a "url=<адрес файла> dest=dest=<путь на удаленной машине>" -b
```
- Пакетный менеджер (Installs, upgrade, downgrades, removes, and lists packages)
    - установка пакета
        ```
        ansible <машина или группа машин или all> -m yum -a "name=<пакет> state=installed" -b
        ```
    - удаление пакета
        ```
        ansible <машина или группа машин или all> -m yum -a "name=<пакет> state=removed" -b
        ```
    Полное описание данного модуля:
    ```
    https://docs.ansible.com/ansible/latest/modules/yum_module.html
    ```
- Проверка коннекта к адресу:
```
ansible <машина или группа машин или all> -m uri -a "url=http://yandex.ru"
```
- Проверка коннекта к адресу с выводом html страницы:
```
ansible <машина или группа машин или all> -m uri -a "url=http://yandex.ru return_content=yes"
```
- Работа с сервисами на удаленной машине:
    - запуск
        ```
        ansible <машина или группа машин или all> -m service -a "name=<имя пакета> state=started enabled=yes" -b
        ```
    Полное описание данного модуля:
    ```
    https://docs.ansible.com/ansible/latest/modules/service_module.html
    ```
##  Playbook

### Запуск playbook
```
ansible-playbook playbook.yml
```

### Пример:

```
# обязательный параметр ---
- name: Install Apache and Upload my Web Page  # имя плейбука
  hosts: all  # все серверы(для каких серверов применяется плейбук)
  become: yes  # аналогично ключу -b в командной строке (запуск через sudo)

  # блок переменных
  vars:
    source_file: ./index.html  # переменная с путем до html файла
    destin_file: /var/www/html  # переменная с путем на удаленном сервере

  # блок тасков
  tasks:
    - name: Install Apache Web Server # имя таска
      yum: name=httpd state=latest  # установка пакета httpd

    - name: Copy Webpage to servers  # Имя пакета
      copy: src={{ source_file }} dest={{ destin_file }} mode=0555  # Копирование файла на сервер
      notify: Restart Apache  # Если операция успешна выполнить handler

    - name: Start WebServer and make it enabled on boot  # Имя таска
      service: name=httpd state=started enabled=yes  # Запуск сервиса httpd

  # блок хендлера
  handlers:
    - name: Restart Apache  # Имя хендлера
      service: name=httpd state=restarted  # Рестарт сервиса httpd
```

# Использованные материалы
```
https://www.youtube.com/playlist?list=PLg5SS_4L6LYufspdPupdynbMQTBnZd31N
```