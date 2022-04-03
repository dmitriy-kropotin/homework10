# homework10
1. Буду использвать следующий Vagrantfile
```
Vagrant.configure("2") do |config|
  config.vm.box = "centos/7"
  config.vm.hostname = "homework06"
  config.vm.network :forwarded_port, guest: 80, host: 8080
  config.vm.network :forwarded_port, guest: 443, host: 8443
  config.vm.synced_folder ".", "/vagrant", disabled: true
end
```
