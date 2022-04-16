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
