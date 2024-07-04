# Домашнее задание к занятию "Disaster recovery и Keepalived" - `Lebedev Boris`


### Инструкция по выполнению домашнего задания

   1. Сделайте `fork` данного репозитория к себе в Github и переименуйте его по названию или номеру занятия, например, https://github.com/имя-вашего-репозитория/git-hw или  https://github.com/имя-вашего-репозитория/7-1-ansible-hw).
   2. Выполните клонирование данного репозитория к себе на ПК с помощью команды `git clone`.
   3. Выполните домашнее задание и заполните у себя локально этот файл README.md:
      - впишите вверху название занятия и вашу фамилию и имя
      - в каждом задании добавьте решение в требуемом виде (текст/код/скриншоты/ссылка)
      - для корректного добавления скриншотов воспользуйтесь [инструкцией "Как вставить скриншот в шаблон с решением](https://github.com/netology-code/sys-pattern-homework/blob/main/screen-instruction.md)
      - при оформлении используйте возможности языка разметки md (коротко об этом можно посмотреть в [инструкции  по MarkDown](https://github.com/netology-code/sys-pattern-homework/blob/main/md-instruction.md))
   4. После завершения работы над домашним заданием сделайте коммит (`git commit -m "comment"`) и отправьте его на Github (`git push origin`);
   5. Для проверки домашнего задания преподавателем в личном кабинете прикрепите и отправьте ссылку на решение в виде md-файла в вашем Github.
   6. Любые вопросы по выполнению заданий спрашивайте в чате учебной группы и/или в разделе “Вопросы по заданию” в личном кабинете.
   
Желаем успехов в выполнении домашнего задания!
   
### Дополнительные материалы, которые могут быть полезны для выполнения задания

1. [Руководство по оформлению Markdown файлов](https://gist.github.com/Jekins/2bf2d0638163f1294637#Code)

---

### Задание 1

Дана схема для Cisco Packet Tracer, рассматриваемая в лекции.
На данной схеме уже настроено отслеживание интерфейсов маршрутизаторов Gi0/1 (для нулевой группы)
Необходимо аналогично настроить отслеживание состояния интерфейсов Gi0/0 (для первой группы).
Для проверки корректности настройки, разорвите один из кабелей между одним из маршрутизаторов и Switch0 и запустите ping между PC0 и Server0.
На проверку отправьте получившуюся схему в формате pkt и скриншот, где виден процесс настройки маршрутизатора.

![alt text](https://github.com/bris91/10-01/blob/1f953a527852672fa38d2a9a40102a96a02e4ef2/2024-06-25_21-58-42.png)

![alt text](https://github.com/bris91/10-01/blob/1f953a527852672fa38d2a9a40102a96a02e4ef2/2024-06-25_22-00-18.png)

![alt text](https://github.com/bris91/10-01/blob/1f953a527852672fa38d2a9a40102a96a02e4ef2/2024-06-25_22-05-22.png)


### Задание 2

Запустите две виртуальные машины Linux, установите и настройте сервис Keepalived как в лекции, используя пример конфигурационного файла.
Настройте любой веб-сервер (например, nginx или simple python server) на двух виртуальных машинах
Напишите Bash-скрипт, который будет проверять доступность порта данного веб-сервера и существование файла index.html в root-директории данного веб-сервера.
Настройте Keepalived так, чтобы он запускал данный скрипт каждые 3 секунды и переносил виртуальный IP на другой сервер, если bash-скрипт завершался с кодом, отличным от нуля (то есть порт веб-сервера был недоступен или отсутствовал index.html). Используйте для этого секцию vrrp_script
На проверку отправьте получившейся bash-скрипт и конфигурационный файл keepalived, а также скриншот с демонстрацией переезда плавающего ip на другой сервер в случае недоступности порта или файла index.html

```
#!/bin/bash
HOST="127.0.0.1"
PORT="80"
FILE=/var/www/html/index.nginx-debian.html
exec 3> /dev/tcp/${HOST}/${PORT}
if [ $? -eq 0 ] && [ -f "$FILE" ] ; then exit 0 ; else exit 1 ; fi"
```


```
global_defs {
    script_user root
    enable_script_security
}

vrrp_script check {
    script "/etc/keepalived/check.sh"
    interval 3
}

vrrp_instance VI_1 {
    state MASTER
    interface enp0s8
    virtual_router_id 15
    priority 100
    advert_int 1

    virtual_ipaddress {
            192.168.0.15/24
        }

        track_script {
           check
        }
}
```



```
global_defs {
    script_user root
    enable_script_security
}

vrrp_script check {
    script "/etc/keepalived/check.sh"
    interval 3
}

vrrp_instance VI_1 {
    state BACKUP
    interface enp0s8
    virtual_router_id 15
    priority 90
    advert_int 1

    virtual_ipaddress {
            192.168.0.15/24
        }

        track_script {
           check
        }
}
```


`При необходимости прикрепитe сюда скриншоты
![Название скриншота 2](ссылка на скриншот 2)`

![alt text](https://github.com/bris91/10-01/blob/1f953a527852672fa38d2a9a40102a96a02e4ef2/%D0%A1%D0%BD%D0%B8%D0%BC%D0%BE%D0%BA%20%D1%8D%D0%BA%D1%80%D0%B0%D0%BD%D0%B0%20%D0%BE%D1%82%202024-06-30%2019-38-01.png)

![alt text](https://github.com/bris91/10-01/blob/1f953a527852672fa38d2a9a40102a96a02e4ef2/%D0%A1%D0%BD%D0%B8%D0%BC%D0%BE%D0%BA%20%D1%8D%D0%BA%D1%80%D0%B0%D0%BD%D0%B0%20%D0%BE%D1%82%202024-06-30%2019-38-16.png)

![alt text](https://github.com/bris91/10-01/blob/1f953a527852672fa38d2a9a40102a96a02e4ef2/%D0%A1%D0%BD%D0%B8%D0%BC%D0%BE%D0%BA%20%D1%8D%D0%BA%D1%80%D0%B0%D0%BD%D0%B0%20%D0%BE%D1%82%202024-06-30%2019-38-46.png)
