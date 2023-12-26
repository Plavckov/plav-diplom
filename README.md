Ключевая задача — разработать отказоустойчивую инфраструктуру для сайта, включающую мониторинг, сбор логов и резервное копирование основных данных. Инфраструктура должна размещаться в Yandex Cloud и отвечать минимальным стандартам безопасности.

## Инфраструктура

### Создайте две ВМ в разных зонах, установите на них сервер nginx, если его там нет. ОС и содержимое ВМ должно быть идентичным, это будут наши веб-сервера.

![alt text](https://github.com/Plavckov/plav-diplom/assets/130914025/bddf62cb-714a-42c2-818c-dc099437a654)
)

### Создайте Target Group, включите в неё две созданных ВМ.

![alt text](https://github.com/Plavckov/plav-diplom/assets/130914025/97af71c4-4f23-460b-8453-6f20f374b963)
)

### Создайте Backend Group, настройте backends на target group, ранее созданную. Настройте healthcheck на корень (/) и порт 80, протокол HTTP.

![alt text](https://github.com/Plavckov/plav-diplom/assets/130914025/63f449e7-4da4-4c4a-9370-d61c505ce6a0)


### Создайте HTTP router. Путь укажите — /, backend group — созданную ранее.

![alt text](https://github.com/Plavckov/plav-diplom/assets/130914025/47499a5f-3c2d-4ce3-89f0-e1d64208f123)
)

### Создайте Application load balancer для распределения трафика на веб-сервера, созданные ранее. Укажите HTTP router, созданный ранее, задайте listener тип auto, порт 80.

![alt text](https://github.com/Plavckov/plav-diplom/assets/130914025/f5defb28-98c5-47de-9fcf-3b805d52aca8)
)

На вирутальных машинах с использованием ansibe [ansible/nginx.yaml](ansible/nginx.yaml) установлен nginx и развернут сайт. 

### Протестируйте сайт curl -v <публичный IP балансера>:80

![alt text](https://github.com/Plavckov/plav-diplom/assets/130914025/e6258813-f0b2-4f7c-9cec-96af6525894c)
)


## Мониторинг

### Создайте ВМ, разверните на ней Zabbix. На каждую ВМ установите Zabbix агенты, настройте агенты на отправление метрик в Zabbix.

![alt text](https://github.com/Plavckov/plav-diplom/assets/130914025/6af081b1-9726-493e-87ba-3afda07f9659)


Zabbix сервер на виртуальной машине развернут с использованием ansibe [ansible/zabbix_server.yaml](ansible/zabbix_server.yaml)



Веб-консоль zabbix доступна из сети интернет по адресу (http://51.250.21.192)

Данные для авторизации:

Логин: Admin

Пароль: zabbix

На вирутальные машины с вебсайтом с использованием ansibe [ansible/zabbix_agent.ayml](ansible/zabbix_agent.yaml) установлены zabbix агенты. 

Настроены дашборды.

![alt text](https://github.com/Plavckov/plav-diplom/assets/130914025/2e97c197-d5e0-4fab-9b0a-ab2cd6f1470f)
)


## Логи

### Cоздайте ВМ, разверните на ней Elasticsearch. Установите filebeat в ВМ к веб-серверам, настройте на отправку access.log, error.log nginx в Elasticsearch.

![alt text](https://github.com/Plavckov/plav-diplom/assets/130914025/79e46e6d-c46d-4eb2-ae53-c0ab0c6c0d23)
)

Elasticsearch на виртуальной машине развернут с использованием ansibe [ansible/elasticsearch.yaml](ansible/elasticsearch.yaml)

Выполнена проверка состояния кластера.

![alt text](https://github.com/rus42/SYS-18_diplom/blob/main/img/cluster_health.png)

Filebeat на виртуальной машине развернут с использованием ansibe [ansible/filebeat.yaml](ansible/filebeat.yaml)

Выполнена тестовая проверка доступности elasticsearch.

![alt text](https://github.com/rus42/SYS-18_diplom/blob/main/img/filebeat_test_vm1.png)

![alt text](https://github.com/rus42/SYS-18_diplom/blob/main/img/filebeat_test_vm2.png)

### Создайте ВМ, разверните на ней Kibana, сконфигурируйте соединение с Elasticsearch.

![alt text](https://github.com/Plavckov/plav-diplom/assets/130914025/49a91851-040f-48f5-bfe7-b6e38a1b4cab)
)

Kibana на виртуальной машине развернута с использованием ansibe [ansible/kibana.yaml](ansible/kibana.yaml)

Веб-консоль kibana доступна из сети интернет по адресу http://51.250.36.191:5601

Данные для авторизации: без авторизации.

Логи отправляются в elasticsearch.

![alt text](https://github.com/Plavckov/plav-diplom/assets/130914025/48503dc8-5431-4a47-87e1-ce927cb82b17)



## Сеть

### Разверните один VPC. Сервера web, Elasticsearch поместите в приватные подсети. Сервера Zabbix, Kibana, application load balancer определите в публичную подсеть. Настройте Security Groups соответствующих сервисов на входящий трафик только к нужным портам.

Настроены группы безопасности.

![alt text](https://github.com/rus42/SYS-18_diplom/blob/main/img/security_group.png)


## Резервное копирование

### Создайте snapshot дисков всех ВМ. Ограничьте время жизни snaphot в неделю. Сами snaphot настройте на ежедневное копирование.

Выполнена настройка резервного копирования.

![alt text](https://github.com/Plavckov/plav-diplom/assets/130914025/35ae44ea-3e86-4566-8b60-6632758becb6)

