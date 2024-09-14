# Отчет №1

## Установка Linux

Для настройки Docker контейнера с VNC-сервером на Ubuntu будем использовать `Dockerfile` и `start-vnc.sh`. Вот инструкции, начиная с самого начала.


1) Создаем папку `folder` в вашей домашней директории или на диске (у меня это диск D).

2) Переходим в созданную папку.

3) Создаем файлы `Dockerfile` и `start-vnc.sh`.

**Содержимое файлов**:

- `Dockerfile`
```bash
# Используем образ Ubuntu
FROM ubuntu:latest

# Устанавливаем необходимые пакеты
RUN apt-get update && apt-get install -y \
    x11vnc \
    xvfb \
    xfce4 \
    tightvncserver \
    dbus-x11 \
    xfce4-terminal \
    openssh-server \
    net-tools \
    nano \
    curl \
    wget \
    zip \
    unzip \
    sudo \
    && apt-get clean

# Создаем папку для пароля VNC
RUN mkdir -p /root/.vnc

# Устанавливаем пароль для VNC
RUN echo "$VNC_PASSWORD" | vncpasswd -f > /root/.vnc/passwd && chmod 600 /root/.vnc/passwd

# Копируем скрипт запуска
COPY vnc-start.sh /usr/local/bin/vnc-start.sh
RUN chmod +x /usr/local/bin/vnc-start.sh

# Настраиваем SSH с переменной для пароля
RUN mkdir /var/run/sshd && echo "root:$SSH_PASSWORD" | chpasswd

# Открываем порты
EXPOSE 5900 22

# Используем ENTRYPOINT для гарантированного запуска скрипта при старте
ENTRYPOINT ["/usr/local/bin/vnc-start.sh"]
```

- `vnc-start.sh`:
  
```bash
#!/bin/bash

# Удаление файлов блокировки
rm -rf /tmp/.X1-lock /tmp/.X11-unix/X1

# Проверка и запуск Xvfb
if pgrep Xvfb > /dev/null; then
    echo "Xvfb уже запущен"
else
    echo "Запуск Xvfb"
    Xvfb :1 -screen 0 1024x768x16 &
fi

# Небольшая задержка
sleep 5

# Проверка и запуск XFCE
if pgrep xfce4-session > /dev/null; then
    echo "XFCE уже запущен"
else
    echo "Запуск XFCE"
    DISPLAY=:1 startxfce4 --replace &
fi

# Небольшая задержка
sleep 10

# Запуск терминала XFCE
DISPLAY=:1 xfce4-terminal &

# Проверка и запуск VNC-сервера
if pgrep x11vnc > /dev/null; then
    echo "VNC-сервер уже запущен"
else
    echo "Запуск VNC-сервера"
    x11vnc -display :1 -forever -loop -listen 0.0.0.0 > /var/log/x11vnc.log 2>&1 &
fi

# Запуск SSH
echo "Запуск SSH сервера"
service ssh start

# Для предотвращения завершения скрипта
tail -f /dev/null
```

4) Собираем докер образ:
> В директории, где находится ваш `Dockerfile`, нужно выполнить следующую команду,
> чтобы собрать Docker - образ:
> ```bash
> docker build -t <example(название вашего образа)> .
> ```

Эта команда создаст Docker-образ с именем `example`


5) Запуск контейнера:
   
После создания образа, запустим контейнер с помощью следующей команды:

```bash
docker run -d -p 5901:5900 -p 2222:22 --name cont --env VNC_PASSWORD=my_vnc_pass --env SSH_PASSWORD=my_ssh_pass example
```

6) Зайдем в контейнер в оболочку bash:

```bash
docker exec -it cont /bin/bash
```


## Основные особенности Linux

- Открытость и бесплатность.
- Многозадачность и многопользовательская работа.
- Высокая стабильность и безопасность.
- Поддержка большого количества инструментов для разработчиков и системных администраторов.

## Популярные дистрибутивы Linux

- Ubuntu
- CentOS
- Fedora
- Debian
- Arch Linux

## Различия между Windows и Linux

- Windows — это коммерческая ОС с графическим интерфейсом, разработанная для обычных пользователей.

- Linux — это свободная ОС с открытым исходным кодом, разработанная для гибкой настройки, часто используется в серверах.

- В Linux больше возможностей для управления системой через командную строку, тогда как Windows больше ориентирована на графический интерфейс.


### Работа с файловой системой

1) Создание структуры каталогов:

Команда:

```bash
mkdir -p ~/work/project/files
```

2) Создание файлов в каталоге `files`:

Команда:

```bash
touch ~/work/project/files/file{1..3}.txt
```

ИЛИ

```bash
touch ~/work/project/files/file1.txt ~/work/project/files/file2.txt ~/work/project/files/file3.txt
```

ИЛИ

```bash
touch ~/work/project/files/{file1.txt,file2.txt,file3.txt}
```

3) Сохраним вывод команды `ls` (список файлов в каталоге `files`) в файл `file_list.txt` в каталоге project.

Команда:

```bash
ls ~/work/project/files > ~/work/project/file_list.txt
```

