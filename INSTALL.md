# Установка модуля DeadLarsen IblockSortFix

Модуль `deadlarsen/iblocksortfix` можно установить несколькими способами в зависимости от ваших потребностей.

## 🚀 Установка через Composer (рекомендуется)

### Быстрая установка

```bash
composer require deadlarsen/iblocksortfix
```

После установки модуль автоматически будет скопирован в `local/modules/deadlarsen.iblocksortfix/` и при возможности установлен в Bitrix.

### 🚀 Первый запуск после установки

После успешной установки через Composer выполните команды для начала работы:

```bash
# В корне вашего Bitrix проекта:

# 1. Просмотрите текущую статистику
php local/modules/deadlarsen.iblocksortfix/cli/sort_fix.php stats

# 2. Проверьте состояние сортировки
php local/modules/deadlarsen.iblocksortfix/cli/sort_fix.php check

# 3. При необходимости создайте бекап
php local/modules/deadlarsen.iblocksortfix/cli/sort_fix.php backup

# 4. Исправьте сортировку (с подтверждением)
php local/modules/deadlarsen.iblocksortfix/cli/sort_fix.php fix --backup

# 5. Проверьте результат
php local/modules/deadlarsen.iblocksortfix/cli/sort_fix.php check
```

### 🐳 Команды для Docker окружения

Если ваш проект запущен в Docker:

```bash
# Замените container_name на имя вашего PHP контейнера
docker exec container_name php local/modules/deadlarsen.iblocksortfix/cli/sort_fix.php stats
docker exec container_name php local/modules/deadlarsen.iblocksortfix/cli/sort_fix.php check
echo "y" | docker exec -i container_name php local/modules/deadlarsen.iblocksortfix/cli/sort_fix.php fix --backup
```

### Ручная активация (если автоустановка не сработала)

1. **Через админ-панель:**
   - Зайдите в админ-панель Bitrix
   - Перейдите в "Настройки" → "Модули"
   - Найдите модуль "Sort Fix" и нажмите "Установить"

2. **Через миграцию:**
   ```bash
   vendor/bin/phinx migrate -c database/phinx.php
   ```

3. **Через CLI:**
   ```bash
   php local/modules/deadlarsen.iblocksortfix/install/index.php
   ```

## 📁 Ручная установка

### 1. Скачивание файлов

```bash
# Клонирование репозитория
git clone https://github.com/deadlarsen/iblocksortfix.git local/modules/deadlarsen.iblocksortfix

# Или скачивание архива
wget https://github.com/deadlarsen/iblocksortfix/archive/main.zip
unzip main.zip -d local/modules/
mv local/modules/sortfix-main local/modules/deadlarsen.iblocksortfix
```

### 2. Установка модуля

Выберите один из способов:

**Способ 1: Через админ-панель**
1. Зайдите в админ-панель Bitrix
2. Перейдите в "Настройки" → "Модули"
3. Найдите модуль "Sort Fix" и нажмите "Установить"

**Способ 2: Через миграцию**
```bash
# Скопируйте миграцию
cp local/modules/deadlarsen.iblocksortfix/database/migrations/20250129120000_install_bitrix_sortfix_module.php database/migrations/

# Выполните миграцию
vendor/bin/phinx migrate
```

**Способ 3: Программная установка**
```php
<?php
require_once $_SERVER["DOCUMENT_ROOT"] . "/bitrix/modules/main/include/prolog_before.php";

use Bitrix\Main\Loader;

// Подключаем и устанавливаем модуль
if (Loader::includeModule('bitrix.sortfix')) {
    echo "Module already installed\n";
} else {
    $installer = new DeadLarsen\IblockSortFix\Install\Index();
    $installer->DoInstall();
    echo "Module installed\n";
}
```

## 🐳 Установка в Docker

### Dockerfile

```dockerfile
FROM your-bitrix-image

# Установка через Composer
COPY composer.json composer.lock ./
RUN composer install --no-dev --optimize-autoloader

# Или ручная установка
COPY . /var/www/html/
RUN chown -R www-data:www-data /var/www/html/local/modules/deadlarsen.iblocksortfix
```

### docker-compose.yml

```yaml
version: '3.8'
services:
  bitrix:
    build: .
    volumes:
      - ./local/modules/deadlarsen.iblocksortfix:/var/www/html/local/modules/deadlarsen.iblocksortfix
    environment:
      - COMPOSER_ALLOW_SUPERUSER=1
    command: |
      bash -c "
        composer require deadlarsen/iblocksortfix &&
        php local/modules/deadlarsen.iblocksortfix/cli/sort_fix.php stats
      "
```

## ⚙️ Настройка после установки

### 1. Проверка установки

```bash
# Через CLI
php local/modules/deadlarsen.iblocksortfix/cli/sort_fix.php stats

# Через веб-интерфейс
# Перейдите в админ-панель → Настройки → Исправление сортировки
```

### 2. Первый запуск

```bash
# Проверить состояние
php local/modules/deadlarsen.iblocksortfix/cli/sort_fix.php check

# Создать бекап
php local/modules/deadlarsen.iblocksortfix/cli/sort_fix.php backup

# Исправить сортировку с бекапом
php local/modules/deadlarsen.iblocksortfix/cli/sort_fix.php fix --backup
```

## 🔧 Настройка автозагрузки (для Composer)

Если вы используете собственный автозагрузчик, добавьте в ваш `composer.json`:

```json
{
    "autoload": {
        "psr-4": {
            "DeadLarsen\\IblockSortFix\\": "vendor/deadlarsen/iblocksortfix/lib/"
        }
    }
}
```

## 🚨 Устранение неполадок

### Проблема: Модуль не устанавливается автоматически

**Решение:**
```bash
# Проверьте права доступа
chmod -R 755 local/modules/deadlarsen.iblocksortfix/

# Установите вручную через админ-панель
# или выполните миграцию
```

### Проблема: CLI команды не работают

**Решение:**
```bash
# Проверьте путь к PHP
which php

# Проверьте DOCUMENT_ROOT
cd /path/to/bitrix/root
php local/modules/deadlarsen.iblocksortfix/cli/sort_fix.php help
```

### Проблема: Нет доступа к админ-панели

**Решение:**
```bash
# Установите через CLI
php -d register_argc_argv=On local/modules/deadlarsen.iblocksortfix/install/index.php

# Или выполните миграцию
vendor/bin/phinx migrate
```

## 📋 Требования

- PHP >= 7.4
- 1C-Bitrix (любая актуальная версия)
- MySQL/MariaDB
- Доступ к файловой системе для записи

## 🔗 Полезные ссылки

- [Документация](README.md)
- [Примеры использования](EXAMPLES.md)
- [Журнал изменений](CHANGELOG.md)
- [Участие в разработке](CONTRIBUTING.md) 