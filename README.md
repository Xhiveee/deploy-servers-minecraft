
# 1. ОБНОВЛЕНИЕ СИСТЕМЫ
`apt update`                    # Получить обновления пакетов
`apt upgrade -y`            # Установить обновления (без подтверждения)

# 2. БАЗОВЫЕ УТИЛИТЫ
`apt install -y htop`         # Мониторинг нагрузки на сервер
`apt install -y nano`         # Текстовый редактор

# 3. FIREWALL
`apt install -y ufw`                               # Установка UFW
`sudo ufw default deny incoming`        # Запретить все входящие по умолчанию
`sudo ufw default allow outgoing`       # Разрешить все исходящие
`sudo ufw allow 22/tcp`                          # SSH (обязательно!)
`sudo ufw allow 32364/tcp`                    # sFTP/SSH на кастомном порту
`sudo ufw allow 25565/tcp`                    # Minecraft сервер
`sudo ufw enable`                                     # Включить фаервол
`sudo ufw disable`                                   # Выключить UFW (если нужно) 


# =======================


# 4. FAIL2BAN
`apt install -y fail2ban`

# Создаем локальную конфигурацию
`sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local`

# Настройка защиты SSH (редактируем jail.local)
`sudo nano /etc/fail2ban/jail.local`

В секции [sshd] изменить:
 enabled = true
 port = 22,32364          # ваши SSH порты
 filter = sshd
 logpath = /var/log/auth.log
 maxretry = 3                # бан после 3 попыток
 bantime = 3600           # бан на 1 час (в секундах)
 findtime = 600             # окно времени для подсчета попыток

# Перезапуск и автозагрузка
`sudo systemctl enable fail2ban`
`sudo systemctl restart fail2ban`
`sudo fail2ban-client status`               # Проверить статус
`sudo fail2ban-client status sshd`       # Проверить статус SSH защиты

# SSH (БЕЗОПАСНАЯ НАСТРОЙКА) 
`sudo nano /etc/ssh/sshd_config`

# РЕКОМЕНДУЕМЫЕ ИЗМЕНЕНИЯ:
 Port 32364                                # Сменить порт (опционально)
 PermitRootLogin no                 # Запретить вход root
 PasswordAuthentication yes    # или no (если используете ключи)
 MaxAuthTries 3                        # Макс. попыток авторизации
 AllowUsers ник1 ник2             # Разрешить только конкретных пользователей

`sudo service ssh restart`             # Перезапуск SSH
`sudo systemctl status ssh`          # Проверка статуса


# =======================


# 5. ПОЛЬЗОВАТЕЛИ (БЕЗОПАСНЫЙ МЕТОД) 


# Создание обычного пользователя 
`sudo useradd -m -s /bin/bash ник`      # -m создает домашнюю папку /home/ник
`sudo passwd ник`                        # Установить пароль (вводите 2 раза)

# Добавление в группу sudo (для админских прав)
`sudo usermod -aG sudo ник`              # Дать права sudo

# Проверка
`groups ник`                             # Проверить группы пользователя
`su - ник`                               # Переключиться на пользователя
`sudo whoami`                            # Должно вывести "root" (проверка sudo)

# Отключение root (после создания sudo-пользователя!)
`sudo passwd -l root`                    # Заблокировать пароль root (безопасно)
 *или полное отключение входа root через SSH (см. выше PermitRootLogin no)*

# Удаление пользователя (если нужно)
`sudo userdel ник`                     # Удалить пользователя
`sudo userdel -r ник`                 # Удалить пользователя и его домашнюю папку


# =======================


# 6. УСТАНОВКА MCSMANAGER
`sudo su -c "wget -qO- https://script.mcsmanager.com/setup.sh | bash"` # скрипт установки
# Сначала запустите службу демона панели.
`systemctl start mcsm-daemon.service`
# Затем запустите веб-службу панели.
`systemctl start mcsm-web.service`

# Команда перезапуска панели
`systemctl restart mcsm-daemon.service`
`systemctl restart mcsm-web.service`

# Команда остановки панели
`systemctl stop mcsm-web.service`
`systemctl stop mcsm-daemon.service`

# Автозапуск MCSManager при загрузке системы
`systemctl enable mcsm-web.service`
`systemctl enable mcsm-daemon.service`

