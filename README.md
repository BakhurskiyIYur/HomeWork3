# Домашнее задание №3

Описание/Пошаговая инструкция выполнения домашнего задания:
* Создайте виртуальную машину c Ubuntu 20.04/22.04 LTS в GCE/ЯО/Virtual Box/докере
* Поставьте на нее PostgreSQL 15 через sudo apt
* Проверьте что кластер запущен через sudo -u postgres pg_lsclusters
  > скрин: <img src="pic/3.JPG" align="center" />
* Зайдите из под пользователя postgres в psql и сделайте произвольную таблицу с произвольным содержимым
  > скрин: <img src="pic/4.jpg" align="center" />
* Остановите postgres (например, через sudo -u postgres pg_ctlcluster 15 main stop)
  > скрин: <img src="pic/5.JPG" align="center" />
* Создайте новый диск к ВМ размером 10GB
  > скрин: <img src="pic/6.JPG" align="center" />
* Добавьте свеже-созданный диск к виртуальной машине - надо зайти в режим ее редактирования и дальше выбрать пункт attach existing disk
  > скрин: <img src="pic/7.JPG" align="center" />
  > скрин: <img src="pic/71.JPG" align="center" />
* 8Проинициализируйте диск согласно инструкции и подмонтировать файловую систему, только не забывайте менять имя диска на актуальное, в вашем случае это скорее всего будет /dev/sdb - https://www.digitalocean.com/community/tutorials/how-to-partition-and-format-storage-devices-in-linux
sudo parted -l | grep Error
sudo parted /dev/vdb mklabel gpt
mkpart primary ext4 0% 100%
sudo mkfs -t ext4 /dev/vdb1
sudo mkdir -p /mt/vdb1
sudo mount -t auto /dev/vdb1 /mt/vdb1
  > Открыл диск хранения, который собираюсь разделить. Затем указываю явно имя диска для разбиения. Создаю таблицу разделов gpt. Cкрин: <img src="pic/82.JPG" align="center" />
  > Указываю явно имя диска для разбиения: <img src="pic/83.JPG" align="center" />
  > скрин: <img src="pic/84.JPG" align="center" />
* Перезагрузите инстанс и убедитесь, что диск остается примонтированным (если не так смотрим в сторону fstab).
<br>__*В файле /etc/fstab добавил строчку "/dev/vdb1 /mnt/vdb1 ext4 defaults 0 0"  для автоматического монтирования диска после перезапуска инстанса.*__
  > скрин: <img src="pic/9.JPG" align="center" />
* Cделайте пользователя postgres владельцем /mnt/data - sudo chown -R postgres:postgres /mnt/vdb1/
* Перенесите содержимое /var/lib/postgres/15 в /mnt/data - sudo mv /var/lib/postgresql/15 /mnt/vdb1
  > скрин: <img src="pic/11.JPG" align="center" />
* Попытайтесь запустить кластер - sudo -u postgres pg_ctlcluster 15 main start
  > скрин: <img src="pic/12.JPG" align="center" />
* Напишите получилось или нет и почему
<br>__*Запустить кластер не получилось, так как необходимые для запуска файлы были перемещены в другую директорию.*__
* Задание: найти конфигурационный параметр в файлах расположенных в /etc/postgresql/15/main который надо поменять и поменяйте его
<br>__*Файл называется postgresql.conf*__
  > скрин: <img src="pic/14.JPG" align="center" />
* Напишите что и почему поменяли
<br>__*В файле postgresql.conf  поменял директорию для параметра: data_directory = '/mnt/vdb1/15/main/'.*__
<br>__*Сделал это потому, что ранее файлы, которые необходимы для запуска кластера располагались в другой директории, а в п.11 эти файлы были перемещены.*__
* Попытайтесь запустить кластер - sudo -u postgres pg_ctlcluster 15 main start
  > скрин: <img src="pic/16.JPG" align="center" />
* Напишите получилось или нет и почему
<br>__*Не получилось запустить кластер.  Потому что после обновления файла postgresql.conf нужно перезапустить виртуальную машину. После перезапуска ВМ - запустился.*__
  > скрин: <img src="pic/17.JPG" align="center" />
* Зайдите через psql и проверьте содержимое ранее созданной таблицы
<br>__*Вставленные мною данные в таблицу присутствуют.*__
  > скрин: <img src="pic/18.JPG" align="center" />

