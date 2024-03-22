# PROXY
apt-get update
apt-get install haproxy
systemctl start haproxy
systemctl enable --now haproxy
Проверим haproxy:
systemctl status haproxy
Создадим резервную копию конфигурационного файла:
cp /etc/haproxy/haproxy.cfg /etc/haproxy/haproxy.cfg.bak
Откроем конф. файл:
vim /etc/haproxy/haproxy.cfg
![image](https://github.com/Petus888/PROXY/assets/163840264/cbbab240-fd41-406d-992f-aa866b4b2255)
systemctl restart haproxy

#3module
![image](https://github.com/Petus888/PROXY/assets/163840264/c1115295-00c8-4210-816b-c5a84b67c7b0)
![image](https://github.com/Petus888/PROXY/assets/163840264/c3349a3c-dcf0-47de-8d47-f7f00a75cf34)
![image](https://github.com/Petus888/PROXY/assets/163840264/8db62524-8129-4089-9397-0577e6cfdfa0)
![image](https://github.com/Petus888/PROXY/assets/163840264/213dd625-4953-4ba9-88e4-568fab91015e)
![image](https://github.com/Petus888/PROXY/assets/163840264/5272caea-3b62-437d-8e24-656740056149)
![image](https://github.com/Petus888/PROXY/assets/163840264/45ca1dab-4944-4f77-84d3-5807fa6509cc)
![image](https://github.com/Petus888/PROXY/assets/163840264/fb2f5888-f9d4-4473-a589-3e301fa141e5)
Разрешим SUDO без пароля для пользователя user 

su -
Вводим пароль
vim /etc/sudoers

user ALL=(ALL:ALL) NOPASSWD: ALL
:wq!
exit

Проверка
sudo vim

Установка Docker и Docker Compose

sudo apt-get update
sudo apt-get install docker-engine docker-compose
sudo usermod user -aG docker
sudo systemctl enable --now docker

Закрываем PuTTY и снова заходим

Развертывание приложений в Docker

Создайте локальный Docker Registry

docker run -d -p 5000:5000 --restart=always --name registry registry:2

Проверка
docker ps -a

В домашней директории хоста создайте файл name.txt и запишите в него строку experts

echo experts > name.txt

Напишите Dockerfile для приложения HelloFIRPO

vim Dockerfile

FROM alpine:latest
WORKDIR /hello
COPY name.txt .
CMD echo "Hello, FIRPO! Greetings from $(cat name.txt)"

docker build -t localhost:5000/app:1.0 .
docker push localhost:5000/app:1.0

Проверка
docker images localhost:5000/app

Создание приложения MediaWiki

vim wiki.yml

version: '3'
services:
  wiki:
    image: mediawiki
    container_name: wiki
    restart: always
    ports:
      - "80:80"
    volumes:
      #- ./LocalSettings.php:/var/www/html/LocalSettings.php
      - images:/var/www/html/images
    networks:
      - wiki-net

  db:
    image: mysql:5.7

container_name: db
    restart: always
    environment:
      MYSQL_DATABASE: mediawiki
      MYSQL_USER: wiki
      MYSQL_PASSWORD: P@ssw0rd
      MYSQL_ROOT_PASSWORD: P@ssw0rd
    volumes:
      - dbvolume:/var/lib/mysql
    networks:
      - wiki-net

volumes:
  images:
  dbvolume:

networks:
  wiki-net:
    driver: bridge

docker-compose -f wiki.yml up -d

docker ps -a

В браузере вводим адрес ControlVM

![image](https://github.com/Petus888/PROXY/assets/163840264/1967353f-0f73-4307-8d32-5f63544d0059)
![image](https://github.com/Petus888/PROXY/assets/163840264/3014053d-82fd-4ca9-8794-26b0e1648d7c)
![image](https://github.com/Petus888/PROXY/assets/163840264/0927f77d-6a86-435a-a8c2-e9dd2d221573)
![image](https://github.com/Petus888/PROXY/assets/163840264/e9235ccc-9762-46f3-861f-0cbd1fa6cd4b)
![image](https://github.com/Petus888/PROXY/assets/163840264/b6eb8950-be2d-48cc-a94f-5f34a86e5ca8)

vim wiki.yml
убираем # в строке  #- ./LocalSettings.php:/var/www/html/LocalSettings.php и снова запускаем сборка docker-compose

docker-compose -f wiki.yml up -d

docker ps -a
![image](https://github.com/Petus888/PROXY/assets/163840264/6b02cec3-9bec-455e-8aab-fb8c4480204f)

Настройка мониторинга с помощью NodeExporter, Prometheus и Grafana
vim monitoring.yml

version: '3.7'
services:
  nodeexporter:
    image: prom/node-exporter
    container_name: nodeexporter
    restart: always
    ports:
      - "9100:9100"
    volumes:
      - /proc:/host/proc:ro
      - /sys:/host/sys:ro
      - /:/rootfs:ro
    command:
      - '--path.procfs=/host/proc'
      - '--path.sysfs=/host/sys'
      - '--collector.filesystem.ignored-mount-points=^/(sys|proc|dev|host|etc)($$|/)'

  prometheus:
    image: prom/prometheus
    container_name: prometheus
    restart: always
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
      - prometheus_data:/prometheus
    ports:
      - "9090:9090"
    command:
      - '--config.file=/etc/prometheus/prometheus.yml'
      - '--storage.tsdb.path=/prometheus'
      - '--web.console.libraries=/usr/share/prometheus/console_libraries'
      - '--web.console.templates=/usr/share/prometheus/consoles'

  grafana:
    image: grafana/grafana
    container_name: grafana
    restart: always
    volumes:
      - grafana_data:/var/lib/grafana
    ports:
      - "3000:3000"
    depends_on:
      - prometheus

volumes:
  prometheus_data:
  grafana_data:

networks:
  default:
    driver: bridge

vim prometheus.yml

scrape_configs:
  - job_name: node
    scrape_interval: 5s
    static_configs:
    - targets: ['172.16.100.27:9100']


docker-compose -f monitoring.yml up -d

В браузере вводим адрес ControlVM:3000 (по умолчанию admin / admin)

Меняем пароль на P@ssw0rd
![image](https://github.com/Petus888/PROXY/assets/163840264/e90a8ddb-61d0-4511-80ee-6ab943161bee)
![image](https://github.com/Petus888/PROXY/assets/163840264/a253f1a3-fe68-4c8e-aaf5-53ebfb191361)
![image](https://github.com/Petus888/PROXY/assets/163840264/59381e54-cdbd-4603-81a0-2a921306d5bb)
![image](https://github.com/Petus888/PROXY/assets/163840264/4477bedd-2d3d-4113-949b-6adf8c1d2a52)
![image](https://github.com/Petus888/PROXY/assets/163840264/047b305e-d0cd-4e6e-be61-9bf929613407)
![image](https://github.com/Petus888/PROXY/assets/163840264/22b4c103-c3f7-459f-af92-08d34d3d850e)
![image](https://github.com/Petus888/PROXY/assets/163840264/e4be493a-5420-48d2-acd4-7a8f39c11e5f)












