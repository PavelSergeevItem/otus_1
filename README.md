
**1. Создание стенда для лаборатоной работы.**

Системный блок: mb — ga-h61m-ds2, cpu — i52500, ram — 8GBram, storage — 120GB ssd, os — ubuntu 22.02.
Установил програмы из методички, vagrant, packer, virtualbox.
Зарегистрировался на гитхабе, вагрант облаке.
Настроил клиент впн.

**2. Обновление ядра**

Находясь в папке с проектом vagrant создал вагрант файл командой ``vagrant init``, код файла взлял из методички.
Далее запустил виртуальную машину командой ``Vagrant up``.
Подключился по ssh, использую команду ``vagrant ssh``.
Обновил ядро, используя команды.
``sudo yum install -y https://www.elrepo.org/elrepo-release-8.el8.elrepo.noarch.rpm``.
``sudo yum --enablerepo elrepo-kernel install kernel-ml -y``.
``sudo grub2-mkconfig -o /boot/grub2/grub.cfg``.
``sudo grub2-set-default 0``.
``sudo reboot``.

**3. Создание образа системы.**

В папке vagrant создал папку packer, используя команду ``mkdir packer``.
В ней создал файл centos.json ``sudo nano centos.json``, используя инструкцию в методичке написал код. Данный файл несет шаги создания образа. Centos.json доступен по ссылки [https://github.com/PavelSergeevItem/otus_1/blob/main/centos.json].
Далее в папке packer создал папку http. В ней создал конфиг файл ks.cfg. ks.cfg — это файл автоматической конфигурации ОС. Файл ks.cfg доступен по ссылки [https://github.com/PavelSergeevItem/otus_1/blob/main/ks.cfg].
Далее в папке packer создал папку scripts, где разместил два скрипта: stage-1-kernel-update.sh содержит команды по обновлению ядра и stage-2-clean.sh, который очистит ненужные файлы из нашей ОС и добавит ssh-ключ пользователя vagrant. Оба файла доступны [https://github.com/PavelSergeevItem/otus_1].
Далее находясь в папке packer создал образ системы, при помощи команды ``packer build centos.json``.
После успешного создания .box файла импортировал его в vagrant командой ``vagrant box add centos8-kernel5 centos-8-kernel-5-x86_64-Minimal.box``
Перейдя в папку vagrrant проверил наличие образа командой ``vagrant box list``.
Создал на основе образа vagrantfile командой ``vagrant init centos8-kernel5``, файл доступен по ссылки [ttps://github.com/PavelSergeevItem/otus_1/blob/main/Vagrantfile.txt].
Запустил вм: ``vagrant up``.
Подключился к ней: ``vagrant ssh``.
Проверил ядро: ``uname -r``.
Вышел: ``exit``.
После удалил вм при помощи команды ``vagrant destory --force``.

**4. Загрузка образа в Vagrant cloud**

Находясь в каталоге packer залогинился в vagrant cloud командой: ``vagrant cloud auth login``.
Далее последовательно указал: мой логин и пароль, далее мой токен.
Получил сообщение: You are now logged in.
Опубликовал мой образ при помощи команды ``vagrant cloud publish --release <user_account>/centos8-kernel5 1.0 virtualbox centos-8-kernel-5-x86_64-Minimal.box``.
Где <user_account> - мой аккаунт в вагрант клауд.
centos8-kernel5 1.0 virtualbox centos-8-kernel-5-x86_64-Minimal.box доступен по ссылки https://app.vagrantup.com/pavelsergeev/boxes/centos8-kernel5, доступен по запросу.


Автор работы доступен по адресу ps@item.expert или в телеграмм [https://t.me/ch33zass.](url)
