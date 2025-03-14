Этот шаг нужен чтобы мы создали разделы, где будет находиться Windows

## Заметки:
> **Внимание!** Если вы удалили любой раздел используя diskpart, Windows рано или поздно отправит команду памяти, которая будет неверно распознана, отчего память будет стёрта.
- Все команды были протестированы.
- Все ваши данные в Android будут удалены
- Игнорируйте предупреждения `udevadm`.
- Не запускайте одну и ту же команду дважды.
- Не перезагружайте телефон если вы совершили ошибку, обратитесь в  [Telegram чат проекта](https://t.me/winonvayu).

#### Загрузите TWRP recovery через комьютер с помощью fastboot
```cmd
fastboot boot <twrp.img>
```
> Если вы уже установили TWRP до этого, просто загрузитесь в него нажав кнопку питания и добавления громкости

#### Размонтирование всех разделов
Перейдите в раздел монтирования и размонтируйте все разделы

## Необходимо переместить нужные утилиты в телефон:
```cmd
adb push parted /sbin
```

## Запустите adb shell:
```cmd
adb shell
```

# Переразметка разделов
#### Дайте правильные разрешения утилитам:
```sh
chmod 755 /sbin/parted
```

### Измените размер таблицы разделов
> Чтобы разделы Windows поместились в таблице разделов
```sh
sgdisk --resize-table 64 /dev/block/sda
```

### Запустите parted
```sh
parted /dev/block/sda
```


### Удалите раздел `userdata`
> Вы должны убедится что 32 это номер раздела `userdata` командой
>  `print all`
```sh
rm 32
```

### Создайте разделы
> Если вы получите какое либо сообщение, говоря вам либо игнорировать либо отменить, просто введите i и нажмите enter.

#### Для моделей 6/128:

- Создайте ESP раздел (будет содержать загрузчик Windows и файлы EFI)
```sh
mkpart esp fat32 11.8GB 12.2GB
```

- Создайте раздел на который будет установлен Windows
```sh
mkpart win ntfs 12.2GB 70.2GB
```

- Создайте раздел `userdata` для использования Android вместе с Windows
```sh
mkpart userdata ext4 70.2GB 127GB
```


#### Для моделей 8/256:

- Создайте ESP раздел (будет содержать загрузчик Windows и файлы EFI)
```sh
mkpart esp fat32 11.8GB 12.2GB
```

- Создайте раздел на который будет установлен Windows
```sh
mkpart win ntfs 12.2GB 132.2GB
```

- Создайте раздел `userdata` для использования Android вместе с Windows
```sh
mkpart userdata ext4 132.2GB 255GB
```


### Сделайте раздел ESP загрузочным, чтобы образ EFI мог его обнаружить
```sh
set 32 esp on
```

### Закройте parted
```sh
quit
```

### Перезагрузитесь в TWRP

### Запустите adb shell снова используя комьютер
```cmd
adb shell
```

### Отформатируйте разделы
-  Отформатируйте раздел ESP в файловую систему FAT32
```sh
mkfs.fat -F32 -s1 /dev/block/by-name/esp
```

-  Отформатируйте раздел Windows в файловую систему NTFS
```sh
mkfs.ntfs -f /dev/block/by-name/win
```

- Отформаируйте userdata
Перейдите в меню "Очистка", выберите "Форматировать data" и напишите `yes`.

### Убедитесь что Android запускается
Просто перезагрузите телефон в систему, и убедитесь что Android запускается.


## [Следующий шаг: Установка Windows](/guide/Russian/2-install-ru.md)
