Домашнее задание к занятию «Репликация и масштабирование. Часть 1» SKOPKIN ILYA

 - Задание 1
На лекции рассматривались режимы репликации master-slave, master-master, опишите их различия.

Ответить в свободной форме.

При режиме master-slave все манипуляции с данными производятся на "мастере", а "слэйв" реплицирует внесённые изменения с "мастера".

Режим master-master по сути тот же master-slave, где "мастеру" добавляется роль "слэйва", и он будет реплицировать данные с того сервера, что был "слэйвом". Работа в данном режиме - нежелательна, т.к. может привести к потерям данных.


 - Задание 2
Выполните конфигурацию master-slave репликации, примером можно пользоваться из лекции.

Приложите скриншоты конфигурации, выполнения работы: состояния и режимы работы серверов.


 - Install mysql на обоих машинах ( MASTER & SLAVE) ----> sudo apt update && sudo apt install mysql-server

mysql port :3306

Master IP: 192.168.0.66 
Slave IP: 192.168.0.27

  - MASTER-DB1

STEP-1: sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf
	
	bin-address  = 192.168.0.66 (Master IP)
	server-id    = 1
        log_bin=/var/log/mysql/mybin.log 
	

save & exit.
	
STEP-3: sudo systemctl restart mysql

STEP-4: sudo systemctl status mysql
 
STEP-5: sudo mysql -u root -p

STEP-6: mysql > CREATE USER 'replication'@'192.168.0.27' IDENTIFIED WITH mysql_native_password BY '22222';     // 192.168.0.27 = Slave IP

STEP-7: mysql > GRANT REPLICATION SLAVE ON *.* TO 'replication'@'192.168.0.27';                                  // 192.168.0.27 = Slave IP
 
STEP-8: mysql > FLUSH PRIVILEGES;

STEP-9: mysql> SHOW MASTER STATUS;

![alt text](https://github.com/matiz86/git_hw-12.6/blob/main/Screenshot_1.jpg)


  - SLAVE-DB2

STEP-1: sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf
	
	bin-address  = 192.168.0.27 (Slave IP)
	server-id    = 2
        log_bin=/var/log/mysql/mybin.log
	
save & Exit.
STEP-2: sudo systemctl restart mysql

STEP-3: sudo mysql -u root -p

STEP-4: mysql> stop slave;

STEP-5: mysql>  CHANGE MASTER TO MASTER_HOST ='192.168.0.66', MASTER_USER ='replication', MASTER_PASSWORD ='22222', MASTER_LOG_FILE = 'mybin.000001', MASTER_LOG_POS = 1892, SOURCE_SSL=1;

STEP-6: mysql> START SLAVE;

STEP-7: mysql > SHOW REPLICA STATUS \G;

![alt text](https://github.com/matiz86/git_hw-12.6/blob/main/Screenshot_2.jpg)

На мастере создаем базу данных

create database hw_12_6_SKOPKIN;

![alt text](https://github.com/matiz86/git_hw-12.6/blob/main/Screenshot_3.jpg)

On Slave 

show databases;

![alt text](https://github.com/matiz86/git_hw-12.6/blob/main/Screenshot_4.jpg)
