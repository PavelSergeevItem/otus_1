------------------------------------------------------------------------------
Создание стенда для лаборатоной работы.
Системный блок: i52500, 8GBram, 120GB ssd, ubuntu 22.02.
Установленны програмы из методички, vagrant, packer, virtualbox.
Зарегистрировался на гитхабе, вагрант облаке.
Настроил клиент впн.
------------------------------------------------------------------------------
**Обновление ядра**
Находясь в папке с проектом vagrant создал вагрант файл командо vagrant init
В вагрант файл внес конфиг

MACHINES = {
  :"kernel-update" => {
              :box_name => "centos/stream8",
              :box_version => "20210210.0",
              :cpus => 2,
              :memory => 1024,
            }
}
Vagrant.configure("2") do |config|
  MACHINES.each do |boxname, boxconfig|
    config.vm.synced_folder ".", "/vagrant", disabled: false
    config.vm.define boxname do |box|
      box.vm.box = boxconfig[:box_name]
      box.vm.box_version = boxconfig[:box_version]
      box.vm.host_name = boxname.to_s
      box.vm.provider "virtualbox" do |v|
        v.memory = boxconfig[:memory]
        v.cpus = boxconfig[:cpus]
      end
    end
  end
end

Далее запустил виртуальную машину Vagrant up.
Подключившись по ssh, использую команду vagrant ssh.
Обновил ядро, используя команды.
sudo yum install -y https://www.elrepo.org/elrepo-release-8.el8.elrepo.noarch.rpm
sudo yum --enablerepo elrepo-kernel install kernel-ml -y
sudo grub2-mkconfig -o /boot/grub2/grub.cfg
sudo grub2-set-default 0
sudo reboot
------------------------------------------------------------------------------
**Создание образа системы.**
В папке vagrant создал папку packer используя команду mkdir packer.
В ней создал файл centos.json, используя инструкцию в методичке написал код. Данный файл несет шаги создания образа.  Centos.json доступен по ссылки https://github.com/PavelSergeevItem/otus_1
Далее в папке packer создал папку http. В ней созал конфиг файл ks.cfg. ks.cfg — это файл автоматической конфигурации ОС. Файл ks.cfg доступен по ссылки https://github.com/PavelSergeevItem/otus_1
Далее в папке packer создал папку script, где разместил два скрипта. stage-1-kernel-update.sh будут содаржаться команды по обновлению ядра и stage-2-clean.sh просто очистит ненужные файлы из нашей ОС и добавит ssh-ключ пользователя vagrant. Оба файла доступны https://github.com/PavelSergeevItem/otus_1
Далее находясь в папке packer создал образ системы, при помощи команды packer build centos.json
После успешного создания .box файла импортировал его в vagrant командой packer build centos.json
Перейдя в папку vagrrant проверил наличие образа командой vagrant box list
Создал на основе образа vagrantfile командой vagrant init centos8-kernel5, файл доступен по ссылки https://github.com/PavelSergeevItem/otus_1
Подключился запустил вм: vagrant up
Подключился к ней: vagrant ssh
Проверил ядро: uname -r
Вышел: exit
После удалил вм при помощи команды vagrant destory --force
------------------------------------------------------------------------------
**Загрузка образа в Vagrant cloud**
Находясь в каталоге packer залогинился в vagrant cloud, при помощи команды vagrant cloud auth login
Далее потребуется последовательно указал:
мой логин и пароль, далее мой токен
Получил сообщение: You are now logged in.
Опубликовал мой образ при помощи команды vagrant cloud publish --release <user_account>/centos8-kernel5 1.0 virtualbox centos-8-kernel-5-x86_64-Minimal.box
Где <user_account> - мой аккаунт в вагрант клауд.
centos8-kernel5 1.0 virtualbox centos-8-kernel-5-x86_64-Minimal.box доступен по ссылки https://app.vagrantup.com/pavelsergeev/boxes/centos8-kernel5
