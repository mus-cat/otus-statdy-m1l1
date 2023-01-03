# otus-statdy-m1l1
Result Homework for month 1 lection 1
## Обновление ядра из сторннего репозитория
1. В Debian 10.13 ядро по умолчанию версии 4.19
2. С сайта проекта xanmod загружаем deb пакет для настройки репозитория данного проекта \
` wget https://dl.xanmod.org/xanmod-repository.deb `
3. Установили данный пакет \
   `dpkg -i xanmod-repository.deb`
4. Обновили список доступных пактов \
   `apt update`
5. Установили ядро 5.10.131-rt72-xanmod1 \
   `apt install linux-image-5.10.131-rt72-xanmod1`
   
## Создание образа (box) для vagrant
1. Получаем имя (**Имя_ВМ**) интересующей нас ВМ в VirtualBox \
`VBoxManager list vms`
2. Создаем box-образ из ВМ \
`vagrant package --base <Имя_ВМ> --output debianBox.box`

## Загрузка образа в vagrant Cloud:
1. Вход на Vagrant Cloud \
`vagrant cloud auth login ...`
2. Загружаем (публикуем) box-образ \
`vagrant cloud publish --release behlc/deb10-xanmod-kernel 1.0 virtualbox debianBox.box`

Vagrant Файл с результатом - **Vagrantfile.repo**
**P.S. SSH вход по ключу может не работать, входить пользователем vagrant, пароль vagrant.**

## Сборка ядра из исходного кода
1. Загружаем исходный код ядра \
`wget https://cdn.kernel.org/pub/linux/kernel/v5.x/linux-5.15.85.tar.xz`

2. Распакавал их в папку /usr/src/
```
cd /usr/src
tar -xJf /home/vagrant/linux-kernel-src/linux-5.15.85.tar.xz
```
3. В директорию с исходниками ядра копируем имеющийся в системе конфиг для ядра
```
cd /usr/src/linux-5.15.85
cp /boot/config-5.10.131-rt72-xanmod1 ./.config
```
4. При необходимости правим конфигурацию ядра \
`make menuconfig`
5. Запустил сборку \
   `make -j 2`
