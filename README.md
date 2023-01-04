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
   
### Создание образа (box) для vagrant
1. Получаем имя (**Имя_ВМ**) интересующей нас ВМ в VirtualBox \
`VBoxManager list vms`
2. Создаем box-образ из ВМ \
`vagrant package --base <Имя_ВМ> --output debianBox.box`

### Загрузка образа в vagrant Cloud:
1. Вход на Vagrant Cloud \
`vagrant cloud auth login ...`
2. Загружаем (публикуем) box-образ \
`vagrant cloud publish --release behlc/deb10-xanmod-kernel 1.0 virtualbox debianBox.box`
2'. (Альтернатива) Загружаем (публикуем) box-образ с помощью web-интерфейса

Vagrant Файл с результатом - **Vagrantfile.xanmod** \
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
6. Устанавливаем (копируем в нужное место) собранные модули ядра \
`make modules_install`
7. Собираем сжатый образ ядра \
`make bzImage`
8. Устанавливаем (копируем в нужно место) ядро со служебными файлами \
`make install`
9. Правим grub (в моём случае, его поправили скрипты при установке ядра)
10. Подключил к ВМ ISO с VirtualBox Additions для версии 6.1.40 (в гостевой ВМ установлена данная версия)
и скопируем отуда файл VBoxLinuxAdditions.run
11. Запустил скрипт на выполнение. В процессе его выполнения будут пересобаны модули Virtual Box Additions под текущую версию ядра \
`./VBoxLinuxAdditions.run`
12. Проверяем возможность монтировать расшаренные папки (в хостовой системе в настройках ВМ создана расшаренная папка **<Share_name>**) \
`mount.vboxsf <Share_name> /mnt`
13. Этап создания box и его загрузки в Vagrant Cloud аналогичен действиям приведенным в разделе **Обновление ядра из сторннего репозитория**  

Vagrant Файл с результатом - **Vagrantfile.sourcecode** \
**P.S. SSH вход по ключу может не работать, входить пользователем vagrant, пароль vagrant**
