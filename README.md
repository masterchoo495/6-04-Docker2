# Домашнее задание к занятию «Docker. Часть 2». Никита Роща

### Задание 1

**Напишите ответ в свободной форме, не больше одного абзаца текста.**

Установите Docker Compose и опишите, для чего он нужен и как может улучшить лично вашу жизнь.

### Ответ:

![png](img1.png)

Docker Compose используется для создания, удаления, сборки, запуска, управления и настройки групп контейнеров с помощью файла конфигурации YAML. Docker Compose упрощает разработку, тестирование, развертывание и настройку, тем самым экономя время на данные операции.

---

### Задание 2

**Выполните действия и приложите текст конфига на этом этапе.**

Создайте файл docker-compose.yml и внесите туда первичные настройки:

* version;
* services;
* volumes;
* networks.

При выполнении задания используйте подсеть 10.5.0.0/16. Ваша подсеть должна называться: <ваши фамилия и инициалы>-my-netology-hw. Все приложения из последующих заданий должны находиться в этой конфигурации.

### Ответ:

```
version: '3'

services:

volumes:

networks:
  roshchanv-my-netology-hw:
    driver: bridge
    ipam:
      config:
        - subnet: 10.5.0.0/16
```

---

### Задание 3

**Выполните действия:**

1. Создайте конфигурацию docker-compose для Prometheus с именем контейнера <ваши фамилия и инициалы>-netology-prometheus.
2. Добавьте необходимые тома с данными и конфигурацией (конфигурация лежит в репозитории в директории 6-04/prometheus).
3. Обеспечьте внешний доступ к порту 9090 c докер-сервера.

### Ответ:

```
version: '3'

services:
  prometheus:
    image: prom/prometheus:v2.47.2
    container_name: roshchanv-netology-prometheus
    command: --web.enable-lifecycle --config.file=/etc/prometheus/prometheus.yml
    ports:
      - 9090:9090
    volumes:
      - ./prometheus:/etc/prometheus
      - prometheus-data:/prometheus
    networks:
      - roshchanv-my-netology-hw
    restart: always

volumes:
  prometheus-data:

networks:
  roshchanv-my-netology-hw:
    driver: bridge
    ipam:
      config:
        - subnet: 10.5.0.0/16
```

---

### Задание 4

**Выполните действия:**

1. Создайте конфигурацию docker-compose для Pushgateway с именем контейнера <ваши фамилия и инициалы>-netology-pushgateway.
2. Обеспечьте внешний доступ к порту 9091 c докер-сервера.

### Ответ:

```
version: '3'

services:
  prometheus:
    image: prom/prometheus:v2.47.2
    container_name: roshchanv-netology-prometheus
    command: --web.enable-lifecycle --config.file=/etc/prometheus/prometheus.yml
    ports:
      - 9090:9090
    volumes:
      - ./prometheus:/etc/prometheus
      - prometheus-data:/prometheus
    networks:
      - roshchanv-my-netology-hw
    restart: always

  pushgateway:
    image: prom/pushgateway:v1.6.2
    container_name: roshchanv-netology-pushgateway
    ports:
      - 9091:9091
    networks:
      - roshchanv-my-netology-hw
    depends_on:
      - prometheus
    restart: unless-stopped

volumes:
  prometheus-data:

networks:
  roshchanv-my-netology-hw:
    driver: bridge
    ipam:
      config:
        - subnet: 10.5.0.0/16
```

---

### Задание 5

**Выполните действия:**

1. Создайте конфигурацию docker-compose для Grafana с именем контейнера <ваши фамилия и инициалы>-netology-grafana.
2. Добавьте необходимые тома с данными и конфигурацией (конфигурация лежит в репозитории в директории 6-04/grafana).
3. Добавьте переменную окружения с путем до файла с кастомными настройками (должен быть в томе), в самом файле пропишите логин=<ваши фамилия и инициалы> пароль=netology.
4. Обеспечьте внешний доступ к порту 3000 c порта 80 докер-сервера.

### Ответ:

```
version: '3'

services:
  prometheus:
    image: prom/prometheus:v2.47.2
    container_name: roshchanv-netology-prometheus
    command: --web.enable-lifecycle --config.file=/etc/prometheus/prometheus.yml
    ports:
      - 9090:9090
    volumes:
      - ./prometheus:/etc/prometheus
      - prometheus-data:/prometheus
    networks:
      - roshchanv-my-netology-hw
    restart: always

  pushgateway:
    image: prom/pushgateway:v1.6.2
    container_name: roshchanv-netology-pushgateway
    ports:
      - 9091:9091
    networks:
      - roshchanv-my-netology-hw
    depends_on:
      - prometheus
    restart: unless-stopped

  grafana:
    image: grafana/grafana
    container_name: roshchanv-netology-grafana
    environment:
      GF_PATHS_CONFIG: /etc/grafana/grafana.ini
    ports:
      - 80:3000
    volumes:
      - ./grafana:/etc/grafana/
      - grafana-data:/var/lib/grafana
    networks:
      - roshchanv-my-netology-hw
    depends_on:
      - prometheus
    restart: unless-stopped

volumes:
  prometheus-data:
  grafana-data:

networks:
  roshchanv-my-netology-hw:
    driver: bridge
    ipam:
      config:
        - subnet: 10.5.0.0/16
```

---

### Задание 6

**Выполните действия.**

1. Настройте поочередность запуска контейнеров.
2. Настройте режимы перезапуска для контейнеров.
3. Настройте использование контейнерами одной сети.
4. Запустите сценарий в detached режиме.

### Ответ:

![png](img2.png)

---

### Задание 7

**Выполните действия.**

1. Выполните запрос в Pushgateway для помещения метрики <ваши фамилия и инициалы> со значением 5 в Prometheus: echo "<ваши фамилия и инициалы> 5" | curl --data-binary @- http://localhost:9091/metrics/job/netology.
2. Залогиньтесь в Grafana с помощью логина и пароля из предыдущего задания.
3. Cоздайте Data Source Prometheus (Home -> Connections -> Data sources -> Add data source -> Prometheus -> указать "Prometheus server URL = http://prometheus:9090" -> Save & Test).
4. Создайте график на основе добавленной в пункте 5 метрики (Build a dashboard -> Add visualization -> Prometheus -> Select metric -> Metric explorer -> <ваши фамилия и инициалы -> Apply.

В качестве решения приложите:

* docker-compose.yml целиком;
* скриншот команды docker ps после запуске docker-compose.yml;
* скриншот графика, постоенного на основе вашей метрики.

### Ответ:

![png](img3.png)

![png](img4.png)

![png](img5.png)

![png](img6.png)

---

### Задание 8

**Выполните действия:**

1. Остановите и удалите все контейнеры одной командой.

В качестве решения приложите скриншот консоли с проделанными действиями.

### Ответ:

![png](img7.png)

---

## Дополнительные задания* (со звёздочкой)
Их выполнение необязательное и не влияет на получение зачёта по домашнему заданию. Можете их решить, если хотите лучше разобраться в материале.

### Задание 9*

**Выполните действия:**

Создайте конфигурацию docker-compose для Alertmanager с именем контейнера <ваши фамилия и инициалы>-netology-alertmanager.
Добавьте необходимые тома с данными и конфигурацией, сеть, режим и очередность запуска.
Обновите конфигурацию Prometheus (необходимые изменения ищите в презентации или документации) и перезапустите его.
Обеспечьте внешний доступ к порту 9093 c докер-сервера.
В качестве решения приложите скриншот с событием из Alertmanager.

### Задание 10*

Запустите свой сценарий на чистом железе без предзагруженных образов.

**Ответьте на вопросы в свободной форме:**

Опишите выполненный вами процесс развертывания сценария.
Как вы думаете зачем может понадобиться такой способ развертывания?
