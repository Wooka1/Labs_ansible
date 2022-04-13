# 5 Лабораторная

# Задания:
-  На серверах web1, web2 установить Nginx.
- На серверах haproxy1, haproxy2 установить и настроить отказоустойчивую связку HAProxy+Keepalived. Настроить VIP с помощью Keepalived в соответствии со схемой
- На серверах web1, web2 Nginx должен работать по порту 8080 и отдавать кастомную страницу, зайдя на которую можно понять на каком сервере вы находитесь.
- На серверах с HAProxy ПО должно обеспечить балансировку нагрузки серверов web1 и web2 в режиме round-robin. Сделать таймауты ожидания ответа web1 и web2 как можно меньше. Скажем, 1-2 секунды
- Установка и настройка всего ПО должна быть обеспечена Ansible-сценарием.
- Все файлы по этому заданию выложить в Github и написать ReadMe со скринами работоспособности и инструкцию по запуску вашего Ansible-сценария

# Выполнение:
- Устанавливаем CentOS в .vagrant.d; Изменяем machine и ip в Vagrantfile.
- Описываем группы хостов в inventory
- Редактируем ansible.cfg, записывая в него ссылку на inventory
- Создаем roles где будут хранится все playbooks и конифуграции для наших webservers и backend-servers.

    - В папке с конфигурацей backend-server (lb_role): 

        - Создаем playbook в папке handlers, на который мы будем иногда обращаться - чтобы перезагрузить keepalived и haproxys
        - Создаем playbook в папке tasks, который стянет и скачает все необходимые пакеты: haproxy, keepalived. Указываем путь куда установить конфигурацию для haproxy и keepalived
        - Создаем папку templates, где будет хранится необходимая конфигурация для haproxy - haproxy.cfg.j2(указываем разрешение j2, так как у нас несколько серверов, и делаем присваивание через цикл) и для keepalived - keepalived.conf.j2
        - Создадим папку vars, где будут порты web-servers и значения для bind в keepalived(backend-servers)

    - В папке с конфигурациями webservers (webserver_role): 

        - Создаем playbook в папке handlers, на который мы будем иногда обращаться - чтобы перезагрузить nginx
        - Создаем playbook в папке tasks, который стянет и скачает все необходимые пакеты: epel-release, nginx. Указываем путь куда установить nginx.conf и перезапускаем его. При желании можно указать и установить index.html.j2
        - Создаем папку templates, где будет хранится необходимая конфигурация для nginx - nginx.conf
        - Создадим папку vars, где будут хранится переменные, которые испольюзуются во многих файлах. Процесс автоматизирован.
- Запускаем playbook, заходим в браузер, вводим адреса всех серверов из inventory и проверяем
 
# Скриншоты:

![Alt text](https://github.com/Wooka1/Labs_ansible/blob/master/4_lab/screenshots/%D0%A1%D0%BD%D0%B8%D0%BC%D0%BE%D0%BA%20%D1%8D%D0%BA%D1%80%D0%B0%D0%BD%D0%B0%20%D0%BE%D1%82%202022-04-13%2016-39-42.png "web1")
![Alt text](https://github.com/Wooka1/Labs_ansible/blob/master/4_lab/screenshots/%D0%A1%D0%BD%D0%B8%D0%BC%D0%BE%D0%BA%20%D1%8D%D0%BA%D1%80%D0%B0%D0%BD%D0%B0%20%D0%BE%D1%82%202022-04-13%2016-41-48.png "web1")
------------------------------------

- Вводим Virtual IP. Если выводит сервер - всё отлично.
- Затем проверяем случай отказа одного из backend-серверов. Выключаем один из них и снова проверяем Virtual IP.
# Скриншот после отключения haproxy1:

![Alt text](https://github.com/Wooka1/Labs_ansible/blob/master/4_lab/screenshots/%D0%A1%D0%BD%D0%B8%D0%BC%D0%BE%D0%BA%20%D1%8D%D0%BA%D1%80%D0%B0%D0%BD%D0%B0%20%D0%BE%D1%82%202022-04-13%2016-52-28.png "web1")