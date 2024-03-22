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


