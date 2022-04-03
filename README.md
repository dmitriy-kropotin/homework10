# homework10
1. Буду использвать следующий Vagrantfile
```
Vagrant.configure("2") do |config|
  config.vm.box = "centos/7"
  config.vm.hostname = "homework10"
  config.vm.synced_folder ".", "/vagrant", disabled: true
end
```
