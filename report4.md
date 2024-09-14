# Отчет №4

## 1. Настройка SSH-сервера

### Установка SSH-сервера

Если на вашей системе SSH-сервер не установлен, выполните следующие команды для установки:

```bash
sudo apt-get update
sudo apt-get install openssh-server
```

### Проверка статуса SSH-сервера

Чтобы убедиться, что SSH-сервер запущен, выполните команду:

Команда:

```bash
service ssh status
```

В контейнерах использование `service ssh status` более распространено, потому что контейнеры редко содержат систему `systemd`, которая требуется для работы команды `systemctl`.


## 2. Генерация SSH-ключей

Для создания пары SSH-ключей на вашем локальном компьютере выполните следующую команду:

Команда:
```bash
ssh-keygen -t rsa -b 4096
```

Эта команда генерирует пару ключей с длиной ключа 4096 бит. При генерации вы можете указать путь для сохранения ключей (по умолчанию: `~/.ssh/id_rsa`) и задать пароль для дополнительной безопасности.

### Копирование публичного ключа на сервер

Для того чтобы скопировать публичный ключ на сервер и настроить авторизацию по ключу, используйте команду:

Команда:
```bash
ssh-copy-id username@server_ip
```

Эта команда копирует публичный ключ на сервер и добавляет его в файл `~/.ssh/authorized_keys` на сервере, что позволяет вам подключаться по SSH без пароля.

Или можно отправить ключ используя команду `docker cp`:

Команда:
```bash
docker cp /local/path/to/id_rsa.pub container_name:/path/to/authorized_keys
```

### Подключение к серверу по SSH

После успешной настройки ключей вы можете подключиться к серверу с помощью команды:

Команда:
```bash
ssh username@server_ip
```

# Работа с процессами и службами

## 1. Создание бесконечного фонового процесса

 - Создадим пример простого бесконечного цикла на `Python`:

Команда:

```python
# script.py
import time

while True:
    print("Running...")
    time.sleep(10)
```

- Запустим этот скрипт в фоновом режиме:

Команда:

```bash
python3 script.py &
```

Символ `&` означает, что процесс будет запущен в фоновом режиме.

## 2. Определение PID процесса

Чтобы найти процесс, который был запущен, используем команду `ps` для отображения списка процессов:

Команда:

```bash
ps aux | grep script.py
```

Эта команда покажет все процессы, содержащие строку `scripts.py`, включая PID процесса (Process ID).

## 3. Остановка процесса

После того как определил PID процесса, можгу завершить его с помощью команды `kill`:

Команда:

```bash
kill <PID>
```

Если процесс не завершится после обычного сигнала kill,можем использовать принудительное завершение с флагом -9:

Команда:

```bash
kill -9 <PID>
```

Эта команда гарантированно завершит процесс с указанным PID.
