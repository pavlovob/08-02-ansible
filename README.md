## 08-02-ansible. Документация на playbook
### Общее описание
1. `inventory` файл содержит 2 группы хостов (по группе на задачу)
2. Хост используется один и тот же (виртуальная машина `virtualbox` под `Ubuntu server 20.04LTS`)
3. файл переменных для каждойй задачи лежит в отдельной папке в `group_vars` с соответствующим именем группы из `inventory`
### Плэйбук в данном репозитории выполняет следующие функции (2 плэй секции):
1. Первый плэй выполняет установку БД clickhouse с созданием БД "logs".
Особенности:
- В связи с раскаткой всего сценария на виртуальную машину на Ubuntu OS 20.04 Server были произведены изменения в сценарии на использование `deb` пакетов и менеджера `apt`.
- Блок хэндлеров с перезапуском сервера убран (почему то не захотел отрабатывать, в связи с чем сервер после установки не стартовал), был добавлен прнудительный блок `service` для перезапуска.
- Создание БД так же при удаленном подключении через Ansible валилось с ошибкой типа `210 Connection refused (localhost:9000)`, вручную на сервере БД создавалась нормально. Блок добавлен в `ignore` для дальнейшего выпонения скрипта.
2. Второй плей создает на сервере папку `/vector`, скачивает по указанному URL на удаленный хост архив с `Vector` и распаковывает его в созданную папку.
Особенности:
- Блоки скачивания и распаковки архива модулями `get_url` и `unarchive` можно было заменить одним блоком модуля `unarchive` с опцией предварительного скачивания  файла вида:
```
     - name: Unarchive a file that needs to be downloaded (added in 2.0)
       ansible.builtin.unarchive:
         src: {{ vector_url }}
         dest: /vector
         remote_src: yes
```