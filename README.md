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
15. 
