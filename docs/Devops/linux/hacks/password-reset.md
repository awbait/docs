# Сброс пароля RHEL/Centos 7-8/RedHat

Перезапускаем машину, при запуске ядра GRUB нажимаем E. Попадаем в редактор параметров загрузки ядра.

Ищем строку которая начинается с "linux" или "linux16", переходим в конец строки нажатием ++ctrl+e++ и дописываем `rd.break`
Тем самым прерывая процедуру загрузки.
Жмем ++ctrl+x++

Попадаем в switch_root

Перемонтируем систему для записи:
``` bash
mount -o remount,rw /sysroot
```

Переходим в среду chroot:
``` bash
chroot /sysroot
```

Сбрасываем пароль:
``` bash
passwd
```
Включаем SElinux relabeling:
``` bash
touch /.autorelabel
```

Выходим введя два раза:
``` bash
exit
```
