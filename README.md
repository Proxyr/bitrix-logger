# bitrix-logger — Claude Code Skill

> **A Claude Code skill for structured PHP logging in [Bitrix24](https://www.bitrix24.com/) projects.**
> Bitrix24 is a widely used Russian CRM/ERP platform built on PHP. This skill sets up a [Monolog](https://github.com/Seldaek/monolog)-compatible logger with configurable levels (`debug` / `info` / `warning` / `error`) and channel support. Log format: `[2026-03-09 12:00:00] channel.ERROR: message {"key":"value"}`.

---

Claude Code skill для настройки системы логирования по уровням в проектах Bitrix24.

Реализует структурированное логирование PHP-скриптов в стиле [Monolog](https://github.com/Seldaek/monolog): каждая запись содержит дату, уровень (`debug` / `info` / `warning` / `error`), сообщение и контекст. Формат записи: `[2026-03-09 12:00:00] channel.ERROR: сообщение {"key":"value"}`. Уровни вывода настраиваются — можно логировать всё или только `error`/`warning`.

## Что умеет

- Настроить систему логирования в Bitrix-проекте с нуля или встроить в готовый файл
- Подключить логирование в компонент, модуль, PHP-скрипт, ваш класс или библиотеку
- Создать Logger как часть модуля — самодостаточный, без зависимости от проектных файлов
- Добавить вкладку **«Логирование»** в настройки модуля в admin-панели: управление уровнями, файлом лога, каналом, просмотр последних записей и кнопка очистки

## Установка

Скачай файл `bitrix-logger.skill` и положи в:

```
~/.claude/skills/bitrix-logger.skill
```

Перезапусти Claude Code. Скилл подхватывается автоматически.

## Использование

Просто напиши в чате с Claude Code:

```
Добавь логирование в ajax/save_fias_address.php
```

```
Настрой логирование с нуля, класс RRK\Logger
```

```
Добавь вкладку логирования в admin-настройки модуля vendor.mymodule
```

Скилл сам определит контекст и предложит подходящий вариант.

## Два варианта Logger

| Вариант | Подходит для | Настройки |
|---------|-------------|-----------|
| **Проектный** | Компоненты, AJAX, скрипты проекта | Константы `LOG_ENABLED`, `LOG_DIR`, `LOG_DEFAULT_LEVELS` в `init.php` |
| **Встроенный в модуль** | Самодостаточные модули Bitrix | `Option::get($moduleId, ...)` — без зависимости от проекта |

Оба варианта могут сосуществовать в одном проекте без конфликтов.

## Уровни логирования

```
debug < info < warning < error
```

- `debug` — промежуточные данные, трассировка
- `info` — шаги выполнения
- `warning` — нештатно, но работает
- `error` — критично, требует внимания

## Пример кода

```php
use RRK\Logger;

$logger = Logger::getInstance('company.addresses')
    ->setDefaultFilename('company.addresses.log');

$logger->info("Компонент запущен", ['params' => $this->arParams]);
$logger->debug("Промежуточное значение", ['data' => $someVar]);
$logger->error("Исключение", ['message' => $e->getMessage()]);
```

Лог-файлы пишутся в `/local/log/`.

## Именование лог-файлов

Скилл придерживается соглашения об именовании:

| Файл | Лог |
|------|-----|
| `ajax/save_fias_address.php` | `ajax_save_fias_address.log` |
| `components/vendor/company.addresses/class.php` | `company.addresses.log` |
| `modules/vendor.module/lib/Handlers/MyHandler.php` | `vendor.module_handlers.log` |
| `php_interface/init.d/12-task-view.php` | `init.d_task-view.log` |

## Состав пакета

```
bitrix-logger/
├── SKILL.md                          # Инструкции для Claude
└── references/
    ├── logger-class-template.md      # Шаблон PHP-класса Logger
    └── module-admin-tab.md           # Шаблон вкладки логирования в options.php
```

## Просмотр логов в Bitrix

Для удобного просмотра лог-файлов прямо в административной панели Bitrix рекомендуем модуль:

**[MACHAON: Просмотр логов](https://marketplace.1c-bitrix.ru/solutions/machaon.logs/)** — поддерживает формат Monolog, подсветку уровней, фильтрацию и постраничный просмотр файлов из `/local/log/`.

## Требования

- Claude Code
- Bitrix24 / 1C-Bitrix, PHP 8.1+
