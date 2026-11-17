# VPN<br>
### Описание/Пошаговая инструкция выполнения домашнего задания:<br>
1) Настроить VPN между двумя ВМ в tun/tap режимах, замерить скорость в туннелях, сделать вывод об отличающихся показателях
2) Поднять RAS на базе OpenVPN с клиентскими сертификатами, подключиться с локальной машины на ВМ<br>
3) Задание со звездочкой<br> Самостоятельно изучить и настроить ocserv, подключиться с хоста к ВМ<br>
_____________________________________________________________________________________________________
## 1.
Используем [Vagrantfile](https://github.com/mrigorevich/otus_vpn/blob/main/Vagrantfile).<br>
Поднимаем стенд:<br>
`vagrant up`<br>
Скопируем ключи, созданные Vagrant в ВМ ansible через ~/shared/ в ~/.ssh/ и установим права 600.<br>
Создаем файлы [hosts.ini](https://github.com/mrigorevich/otus_vpn/blob/main/hosts.ini) и [playbook.yml](https://github.com/mrigorevich/otus_vpn/blob/main/playbook.yml) <br>
Запускаем плейбук для первого задания:<br>
`ansible-playbook -i hosts.ini playbook.yml`<br>
Замеряем скорость TAP-туннеля:<br>
На ВМ server запустим iperf в режиме сервера:<br>
`iperf3 -s`<br>
На ВМ client в режиме клиента:<br>
`iperf3 -c 10.10.10.1 -t 60 -i 10`<br>
Зафиксируем результат:<br><br>
<img width="974" height="351" alt="image" src="https://github.com/user-attachments/assets/c4f32880-adc3-46dd-86b9-efb5a1110d23" /><br><br>
Поменяем тип туннеля через плейбук [playbook_tun.yml](https://github.com/mrigorevich/otus_vpn/blob/main/playbook_tun.yml) на ВМ ansible:<br>
`ansible-playbook -i hosts.ini playbook_tun.yml`<br>
И повторим тесты:<br><br>
<img width="974" height="347" alt="image" src="https://github.com/user-attachments/assets/c063470a-2547-42da-b87b-0e24cc0df7ab" /><br><br>
Видим, что в случае с TUN-туннелем за тот же период времени объем переданных на сервер данных на 3% больше, чем в режиме TAP. Так же в режиме TUN меньшее количество ретрансмитов, а значит – стабильнее канал (если можно делать выводы о стабильности канала между двумя ВМ на одном хосте).<br><br>
Задание 1 готово.

## 2.
Используем ВМ server из поднятого стенда, ВМ client уберем<br>
`vagrant halt client`<br><br>
На ВМ ansible запускаем плейбук [playbook_ras.yml](https://github.com/mrigorevich/otus_vpn/blob/main/playbook_ras.yml)<br>
`ansible-playbook -i hosts.ini playbook_ras.yml`<br><br>
Указываем в настройках VirtualBox параметр «Виртуальный адаптер» для интерфейса 192.168.56.10:<br><br>
<img width="948" height="152" alt="image" src="https://github.com/user-attachments/assets/9765ed0b-fb5b-44fc-ac6b-7cf283f615ee" /><br><br>
На хосте (Windows с установленным OpenVPN клиентом) делаем [конфиг](https://github.com/mrigorevich/otus_vpn/blob/main/otus.ovpn):<br><br>
<img width="892" height="477" alt="image" src="https://github.com/user-attachments/assets/925db862-b452-4b8f-96d0-32e9b6ae3d0a" /><br><br>
Копируем ключи из примонтированного к ВМ server ~/shared в папку с конфигом. Запускаем:<br>
`openvpn --config "c:\Program Files\OpenVPN\config\otus.ovpn"`<br><br>
<img width="974" height="241" alt="image" src="https://github.com/user-attachments/assets/a5df8fc3-6f11-4ce6-a05e-f447130189e2" /><br><br>
Проверяем:<br><br>
<img width="974" height="210" alt="image" src="https://github.com/user-attachments/assets/5f089dd1-5b42-4ed7-98db-1e01c7bcdadf" /><br><br>
<img width="974" height="261" alt="image" src="https://github.com/user-attachments/assets/97630f04-ee25-4798-914c-610f671d5c8e" /><br><br>
Задание 2 готово.

## 3.
На это задание пока категорически не хватает времени ☹


