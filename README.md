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

5. Все монтирования на месте. Пробую создать файл и вижу, что файловая система в режиме RO. 

![image](https://user-images.githubusercontent.com/98701086/163937733-884f0621-c780-4049-aeac-5643a9f49708.png)

6. Перемонтирую систему в RW и пробую создать файл, операция выполняется успешно

![image](https://user-images.githubusercontent.com/98701086/163937982-9aae4244-3bad-420b-b579-03e7b3d05705.png)

7. Теперь добавляю параметры загрузки ` rd.break

![image](https://user-images.githubusercontent.com/98701086/163938291-e1e4b2ca-19ef-4ec6-bdd1-accb51a3d2d9.png)

8. Нажимаю Control+X, попадаю в emergency mode

![Снимок экрана 2022-04-16 в 18 23 56](https://user-images.githubusercontent.com/98701086/163938399-3142754b-2a22-4797-ac61-e62d167bf543.png)

9. 
