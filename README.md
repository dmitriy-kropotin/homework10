# homework10
1. Буду использвать следующий Vagrantfile
```
Vagrant.configure("2") do |config|
  config.vm.box = "generic/rocky8"
  config.vm.hostname = "homework10"
  config.vm.synced_folder ".", "/vagrant", disabled: true
end
```
2. Проверяю способ входа в систему без пароля. Добавляю в меню загрузки команду `init=/bin/sh`

![Снимок экрана 2022-04-16 в 18 12 05](https://user-images.githubusercontent.com/98701086/163683457-dcb5dfcb-31da-4242-bc64-8ffce7b0c24a.png)

3. Нажимаю Control+X, происходит загрузка системы, попадаю в sh.

![Снимок экрана 2022-04-16 в 18 14 23](https://user-images.githubusercontent.com/98701086/163683481-96bb73d5-2faf-46bb-b592-8924077e5409.png)

4. Смотрю, что есть через `df -h`, 

![Снимок экрана 2022-04-16 в 18 16 55](https://user-images.githubusercontent.com/98701086/163937520-6f2b350d-aa85-4aa3-a8e9-0cfa9b237766.png)

5. Все монтирования на месте. Пробую создать файл и вижу, что файловая система в режиме `ro`. 

![image](https://user-images.githubusercontent.com/98701086/163937733-884f0621-c780-4049-aeac-5643a9f49708.png)

6. Перемонтирую систему в RW и пробую создать файл, операция выполняется успешно

![image](https://user-images.githubusercontent.com/98701086/163937982-9aae4244-3bad-420b-b579-03e7b3d05705.png)

7. Теперь добавляю параметры загрузки ` rd.break

![image](https://user-images.githubusercontent.com/98701086/163938291-e1e4b2ca-19ef-4ec6-bdd1-accb51a3d2d9.png)

8. Нажимаю Control+X, попадаю в emergency mode

![Снимок экрана 2022-04-16 в 18 23 56](https://user-images.githubusercontent.com/98701086/163938399-3142754b-2a22-4797-ac61-e62d167bf543.png)

9. Проверяю монтирования, вижу что все на месте, вижу что корень системы смотрирован в /sysroot

![image](https://user-images.githubusercontent.com/98701086/163942013-8f1993b4-5677-4620-90fd-256110c7ff8b.png)

10. Меняю root в корень системы, и пробую сменить пароль на root

![image](https://user-images.githubusercontent.com/98701086/163942347-e95cb83f-9ed9-4789-af99-fd2ada960d56.png)

11. Все успешно. (файл /.autorelabel нужен, что бы SElinux перемаркировал всю файловую систему, зачем это ? )
12. Теперь пробую вариант добавить опцию `init=/sysroot/bin/sh` и поменять режим монтирования root с `ro` на `rw`

![Снимок экрана 2022-04-16 в 18 32 19](https://user-images.githubusercontent.com/98701086/163985909-832b97ac-49cc-4c24-8d75-c72b952b2b09.png)

13. Загрузился в emergency mode, и режим монтирования сразу `rw`

![Снимок экрана 2022-04-16 в 18 33 56](https://user-images.githubusercontent.com/98701086/163986221-a4c99d8e-3be0-4ffc-af0d-1402e1b7fc20.png)

14. Теперь попробую переименовать VG. Текущее состояние `vgs`

```
[root@homework10 ~]# vgs
  VG        #PV #LV #SN Attr   VSize    VFree
  rl_rocky8   1   2   0 wz--n- <127.00g <54.94g
  ```
15. Переименовываю `vgrename rl_rocky8 OTUS_ROOT`

```
[root@homework10 ~]# vgrename rl_rocky8 OTUS_ROOT
  Volume group "rl_rocky8" successfully renamed to "OTUS_ROOT"
[root@homework10 ~]# vgs
  VG        #PV #LV #SN Attr   VSize    VFree
  OTUS_ROOT   1   2   0 wz--n- <127.00g <54.94g
```

16. Редактирую fstab `vim /etc/fstab`

```
/dev/mapper/OTUS_ROOT-root /                       xfs     defaults        0 0
UUID=27614e01-943c-47ba-a6e7-06c4ce2ded26 /boot                   xfs     defaults        0 0
/dev/mapper/OTUS_ROOT-swap none                    swap    defaults        0 0
```

17. Редактирую файл настроек GRUB `vim /etc/default/grub`

```
GRUB_CMDLINE_LINUX="no_timer_check crashkernel=auto resume=/dev/mapper/rl_rocky8-swap rd.lvm.lv=OTUS_ROOT/root rd.lvm.lv=rl_rocky8/swap biosdevname=0 net.ifnames=0 rhgb quiet"
```

18. Редактирую файл текущей конфигурации GRUB `vim /boot/grub2/grub.cfg`

```
if [ -z "${kernelopts}" ]; then
  set kernelopts="root=/dev/mapper/OTUS_ROOT-root ro no_timer_check crashkernel=auto resume=/dev/mapper/OTUS_ROOT-swap rd.lvm.lv=OTUS_ROOT/root rd.lvm.lv=OTUS_ROOT/swap biosdevname=0 net.ifnames=0 rhgb quiet "
fi
```

19. Пересоздаю /boot/initramfs `mkinitrd -f -v /boot/initramfs-$(uname -r).img $(uname -r)`

```
[root@homework10 ~]# mkinitrd -f -v /boot/initramfs-$(uname -r).img $(uname -r)
Creating: target|kernel|dracut args|basicmodules
dracut: Executing: /usr/bin/dracut -v -f /boot/initramfs-4.18.0-348.20.1.el8_5.x86_64.img 4.18.0-348.20.1.el8_5.x86_64
...
dracut: *** Stripping files ***
dracut: *** Stripping files done ***
dracut: *** Creating image file '/boot/initramfs-4.18.0-348.20.1.el8_5.x86_64.img' ***
dracut: *** Creating initramfs image file '/boot/initramfs-4.18.0-348.20.1.el8_5.x86_64.img' done ***
```
20. ...и система не загрузилась, перешла в emergency mode. Перезагружаюсь, и вижу, что параметры запуска не изменились...
21. Редактирую параметр запуска ядра вручную, запускаю систему. Делаю `ls -la /boot/grub2/`

```
[root@homework10 ~]# ls -la /boot/grub2/
total 32
drwx------. 4 root root   83 Apr 19 13:09 .
dr-xr-xr-x. 5 root root 4096 Mar 22 19:58 ..
-rw-r--r--. 1 root root   64 Apr 19 13:07 device.map
drwxr-xr-x. 2 root root   25 Mar 22 19:44 fonts
-rw-r--r--. 1 root root 6654 Apr 19 13:07 grub.cfg
-rw-------. 1 root root 1025 Apr 19 13:09 grubenv
drwxr-xr-x. 2 root root 8192 Mar 22 19:44 i386-pc
```

22. Смотрю содеримое grubenv, в нем старое название VG, редактирую `vim /boot/grub2/grubenv` 

```
# GRUB Environment Block
saved_entry=6212f99182784886ad1fb8010eb3543e-4.18.0-348.20.1.el8_5.x86_64
kernelopts=root=/dev/mapper/OTUS_ROOT-root ro no_timer_check crashkernel=auto resume=/dev/mapper/OTUS_ROOT-swap rd.lvm.lv=OTUS_ROOT/root rd.lvm.lv=OTUS_ROOT/swap biosdevname=0 net.ifnames=0 rhgb quiet
boot_success=1
boot_indeterminate=0
#####################
```
23. Перезагружаюсь, система загрузилась! 

```
[root@homework10 ~]# vgs
  VG        #PV #LV #SN Attr   VSize    VFree
  OTUS_ROOT   1   2   0 wz--n- <127.00g <54.94g
```
24. Теперь добавляю свой модуль загрузки. Создаю папку для модуля, и качаю файлы модуля

```
[root@homework10 ~]# mkdir /usr/lib/dracut/modules.d/01tesla
[root@homework10 ~]# cd /usr/lib/dracut/modules.d/01tesla

```
25. Создаю файлы модуля и копирую в них содержимое

```
[root@homework10 01tesla]# ls -la
total 12
drwxr-xr-x.  2 root root   44 Apr 19 13:24 .
drwxr-xr-x. 74 root root 4096 Apr 19 13:18 ..
-rw-r--r--.  1 root root  126 Apr 19 13:24 module-setup.sh
-rw-r--r--.  1 root root  333 Apr 19 13:24 test.sh
[root@homework10 01tesla]# cat module-setup.sh test.sh
#!/bin/bash

check() {
    return 0
}

depends() {
    return 0
}

install() {
    inst_hook cleanup 00 "${moddir}/test.sh"
}
#!/bin/bash

exec 0<>/dev/console 1<>/dev/console 2<>/dev/console
cat <<'msgend'
Hello! You are in dracut module!
 ___________________
< I'm dracut module >
 -------------------
   \
    \
        .--.
       |o_o |
       |:_/ |
      //   \ \
     (|     | )
    /'\_   _/`\
    \___)=(___/
msgend
sleep 10
echo " continuing...."

```

26. Пересобираю initrd 

```
[root@homework10 01tesla]# dracut -f -v
```
27. Проверяю наличие модуля

```
[root@homework10 01tesla]#  lsinitrd -m /boot/initramfs-$(uname -r).img | grep tesla
tesla
```

28. Перезагружаюсь, и временно отключаю "тихую" загрузку

![VirtualBox_homework10_default_1650371690196_51831_19_04_2022_16_51_49](https://user-images.githubusercontent.com/98701086/164020108-2c334614-51fb-4e70-910c-23cb5f3cefa1.png)

29. Ура! Модуль сработал!

![VirtualBox_homework10_default_1650371690196_51831_19_04_2022_16_52_04](https://user-images.githubusercontent.com/98701086/164019918-87652575-7dde-4b6a-bc24-d0a4df38625c.png)

