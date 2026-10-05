---
modified: 2026-10-05T23:24:55+03:00
created: 2026-10-05T20:01:55+03:00
---
Уникальный шифр студента: `372316`

<img width="320" height="207" alt="image" src="https://github.com/user-attachments/assets/8e3e20c9-c91b-4f3d-92a2-c4155a56054d" />


## Раздел 1. Создание пользователя (5 баллов)

### 1.1. Создание пользователя

> [!question] Задание
> Создайте обычного пользователя user1 с идентификатором 1234.
> 
> Пользователь должен входить в группу students.
> 
> Настройте необходимость изменения пароля пользователя user1 каждые три месяца.

Создаем группу studenеs:
`# groupadd students`

Создаем пользователя:
```
# useradd -u 1234 -g students user1

где
-u: uid пользователя
-g: добавляем в дополнительную (второстепенную) группу
```

Максимальный срок,#в теч которого нужно поменять пароль
`# chage -M 90`

## Раздел 2. Мониторинг файлов и процессов (5 баллов)

### 2.1. Мониторинг файлов

> [!question] Задание
> Найдите в системе все файлы с установленным битом set-UID.

```
20:37:09 root@vps-1.1 dmitry find / -perm -4000 -type f 2>/dev/null

/usr/bin/chage
/usr/bin/chfn
/usr/bin/chsh
/usr/bin/gpasswd
/usr/bin/grub2-set-bootflag
/usr/bin/mount
/usr/bin/newgrp
/usr/bin/pam_timestamp_check
/usr/bin/passwd
/usr/bin/pkexec
/usr/bin/su
/usr/bin/sudo
/usr/bin/umount
/usr/bin/unix_chkpwd
/usr/lib/polkit-1/polkit-agent-helper-1

где 
find / ищем по корню
-perm -4000 поиск по биту set-uid
2>/dev/null ошибки вываливаем вникуда
```

### 2.2. Мониторинг процессов

> [!question]
> Найдите в системе все процессы, у которых эффективный UID равен 0, а реальный UID относится к обычным пользователям.
> 
> Если таких процессов в данный момент в системе нет, то на одном терминале начните менять пароль с помощью команды passwd, а на другом терминале осуществите поиск процессов с повышенными привилегиями.

```
20:57:56 root@vps-1.1 dmitry ps ax -o pid,ruid,euid,cmd | awk '$2>=1000 && $3==0' > proc-euid.out
20:59:36 root@vps-1.1 dmitry cat proc-euid.out 

  15925  1000     0 su
```
## Раздел 3. Изучение механизма set-UID (5 баллов)

### 3.1. Выбор утилиты

> [!question]
> Выберите существующую утилиту (команду BASH).
> 
> 
> Придумайте операцию, которая для своего выполнения требует права root.
> 
> Например, чтение файла /etc/passwd командой cat.

`cat`
### 3.2. Выполнение привилегированной операции с помощью механизма set-UID

> [!question]
> Скопируйте утилиту в директорию пользователя.
> 
> С помощью механизма set-UID обеспечьте выполнение утилитой привилегированной операции.
> 
> Убедитесь, что операция выполняется успешно.

```
21:43:12 root@vps-1.1 homework-1 cp /usr/bin/cat .
21:45:52 root@vps-1.1 homework-1 chmod 4755 ./cat
21:48:31 root@vps-1.1 homework-1 su dmitry
21:48:58 dmitry@vps-1.1 homework-1 cat /etc/shadow
cat: /etc/shadow: Permission denied

21:48:58 dmitry@vps-1.1 homework-1 cat /etc/shadow
cat: /etc/shadow: Permission denied

21:49:09 dmitry@vps-1.1 homework-1 ./cat /etc/shadow                                                          
root:?:20723::::::
bin:!*:20470::::::
daemon:!*:20470::::::
adm:!*:20470::::::
lp:!*:20470::::::
sync:!*:20470::::::
shutdown:!*:20470::::::
halt:!*:20470::::::
mail:!*:20470::::::
operator:!*:20470::::::
games:!*:20470::::::
ftp:!*:20470::::::
nobody:!*:20470::::::
systemd-oom:!*:20721::::::
tss:!*:20721::::::
systemd-coredump:!*:20721::::::
systemd-timesync:!*:20721::::::
dbus:!*:20721::::::
polkitd:!*:20721::::::
sshd:!*:20721::::::
chrony:!*:20721::::::
systemd-resolve:!*:20721::::::
dmitry:?:20723:0:99999:7:::
user1:!:20731:0:90:7:::
```


## Раздел 4. Изучение механизма привилегий (5 баллов)

### 4.1. Выбор утилиты

> [!question]
> Выберите существующую утилиту (команду BASH).
> 
> Придумайте операцию, которая для своего выполнения требует права root.
> 
> Например, изменение владельца файла на произвольного пользователя командой chown.

`chown`
### 4.2. Выполнение привилегированной операции с помощью механизма привилегий

> [!question]
> Скопируйте утилиту в директорию пользователя.
> 
> С помощью механизма привилегий обеспечьте выполнение утилитой привилегированной операции.
> 
> Убедитесь, что операция выполняется успешно.

```
21:57:12 root@vps-1.1 homework-1 cp /usr/bin/chown ./
22:47:43 root@vps-1.1 homework-1 touch test.file
22:49:38 root@vps-1.1 homework-1 setcap cap_chown+ep ./chown
22:48:39 root@vps-1.1 homework-1 ls -l
total 116
-rwsr-xr-x 1 root root 40696 Oct  5 21:43 cat
-rwxr-xr-x 1 root root 69984 Oct  5 21:57 chown
-rw-r--r-- 1 root root    23 Oct  5 20:59 proc-euid.out
-rw-r--r-- 1 root root     0 Oct  5 22:48 test.file

22:50:33 root@vps-1.1 homework-1 su dmitry
22:50:38 dmitry@vps-1.1 homework-1 ./chown dmitry:dmitry test.file 
22:51:07 dmitry@vps-1.1 homework-1 ls -l
total 116
-rwsr-xr-x 1 root   root   40696 Oct  5 21:43 cat
-rwxr-xr-x 1 root   root   69984 Oct  5 21:57 chown
-rw-r--r-- 1 root   root      23 Oct  5 20:59 proc-euid.out
-rw-r--r-- 1 dmitry dmitry     0 Oct  5 22:48 test.file

```

## Раздел 5. Изучение механизма sudo (5 баллов)

> [!question]
> С помощью механизма sudo разрешите пользователю user1 устанавливать (изменять) системное время.

```
23:07:51 root@vps-1.1 homework-1 echo 'user1 ALL=(root) /usr/bin/timedatectl' >> /etc/sudoers
23:10:55 root@vps-1.1 homework-1 su user1
[user1@vps-1 homework-1]$ sudo timedatectl set-time '2099-10-09 20:00:00'
20:00:23 root@vps-1.1 homework-1 date    
Fri Oct  9 08:00:26 PM MSK 2099

```

