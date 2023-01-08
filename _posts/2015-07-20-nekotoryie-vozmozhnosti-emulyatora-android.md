---
id: 1506
title: 'Некоторые возможности эмулятора Android'
date: '2015-07-20T20:10:10+00:00'
author: serge
layout: post
guid: 'http://sotnyk.com/?p=1506'
permalink: /2015/07/20/nekotoryie-vozmozhnosti-emulyatora-android/
---

Как я уже писал, периодически прохожу различные курсы он-лайн. Это дает возможность подсмотреть что-то новое, слегка освежить английский язык. Часто выбираю что-то не слишком сложное, что не отнимет много времени. Вот сейчас прошел 4-х недельный курс [Programming Mobile Applications for Android Handheld Systems](https://www.coursera.org/course/androidpart1). В общем, ничего такого сильно интересного в нем не обнаружил, за исключением одной штуки.

Эта штука – управление эмулятором Андроид через telnet. Оставлю небольшой конспект команд здесь для себя, но может еще кому-то будет полезно.

Прежде всего, как подключиться. У вас в системе должен стоять компонент telnet, либо быть установлен какой-то сторонний:

[![telnet-component](https://sotnyk.github.io/wp-content/uploads/2015/07/telnet-component.jpg)](https://sotnyk.github.io/wp-content/uploads/2015/07/telnet-component.jpg)

Далее считаем, что мы находимся в Android Studio.

Запускаем эмулятор андроид-устройства. Обращаем внимание на заголовок его окна:

[![emu-title](https://sotnyk.github.io/wp-content/uploads/2015/07/emu-title.jpg)](https://sotnyk.github.io/wp-content/uploads/2015/07/emu-title.jpg)

Первые 4 цифры – это порт, на котором он «слушает» команды. Теперь можем запускать telnet.

Можно сделать это отдельно, а можно прямо в закладке Terminal Android Studio:

[![terminal-console-android-studio](https://sotnyk.github.io/wp-content/uploads/2015/07/terminal-console-android-studio.jpg)](https://sotnyk.github.io/wp-content/uploads/2015/07/terminal-console-android-studio.jpg)

Набираем здесь:

```
telnet localhost 5554
```

В окне терминала наблюдаем:

[![telnet-connected](https://sotnyk.github.io/wp-content/uploads/2015/07/telnet-connected.jpg)](https://sotnyk.github.io/wp-content/uploads/2015/07/telnet-connected.jpg)

Чтобы получить список того, что мы можем «вытворить» с эмулятором, выполним предложенную команду «help»:

```
Android console command help:

    help|h|?         print a list of commands
    event            simulate hardware events
    geo              Geo-location commands
    gsm              GSM related commands
    cdma             CDMA related commands
    kill             kill the emulator instance
    network          manage network settings
    power            power related commands
    quit|exit        quit control session
    redir            manage port redirections
    sms              SMS related commands
    avd              control virtual device execution
    window           manage emulator window
    qemu             QEMU-specific commands
    sensor           manage emulator sensors
    finger           manage emulator finger print

try 'help <command></command>' for command-specific help
```

Более детальное описание мы можем получить, выполнив соответствующую команду. Пример:

```
help event
allows you to send fake hardware events to the kernel

available sub-commands:
   event send             send a series of events to the kernel
   event types            list all <type> aliases
   event codes            list all <code> aliases for a given <type>
   event text             simulate keystrokes from a given text
```

Для меня были интересными команды управления состоянием питания:

```
help power
allows to change battery and AC power status

available sub-commands:
   power display          display battery and charger state
   power ac               set AC charging state
   power status           set battery status
   power present          set battery present state
   power health           set battery health state
   power capacity         set battery capacity state
```

Например, если нам необходимо проверить, как ведет себя программа при заряде батареи в 10%, выполним такую команду:

```
power capacity 10
```

Если необходимо имитировать, что устройство не подключено к зарядке, выполняем:

```
power ac off
```

А как проверить реакцию на приход SMS? Вот так:

```
sms send 777888999 some sms text
```

В эмуляторе увидим, что пришло sms с номера 777888999.

Включим доступность только 2g-сетей:

```
network speed gsm
```

Включим 3g-сети:

```
network speed umts
```

И так далее.