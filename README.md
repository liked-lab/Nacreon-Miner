# Nacreon Miner 0.23.0

Майнер Pearl (PRL) для NVIDIA GeForce RTX 50 на Linux и HiveOS.
Разработчик — **liked**. Комиссия разработчика — **0,5%**; комиссия пула отдельно.

## Скачать

- **HiveOS:** `Nacreon_hiveos_custom-0.23.0.tar.gz`
- **Linux:** `Nacreon_linux-0.23.0.tar.gz`

Файлы: [официальные релизы](https://github.com/liked-lab/Nacreon-Miner/releases).
Перед установкой [проверьте подпись и SHA-256](https://github.com/liked-lab/Nacreon-Miner/blob/main/SIGNATURES.md).

## Linux

Нужны 64-разрядный Linux и совместимый драйвер NVIDIA. Распакуйте архив:

```bash
tar -xzf Nacreon_linux-0.23.0.tar.gz
cd nacreon
```

Запустите майнер со своим кошельком и именем воркера:

```bash
./nacreon --pool prl.kryptex.network:7048 --wallet YOUR_PEARL_WALLET --worker rig01 --password x
```

| Параметр | Назначение | Пример |
|---|---|---|
| `--pool` | Адрес и порт пула | `--pool prl.kryptex.network:7048` |
| `--wallet` | Ваш Pearl-кошелёк | `--wallet YOUR_PEARL_WALLET` |
| `--worker` | Имя воркера | `--worker rig01` |
| `--password` | Пароль пула | `--password x` |
| `--devices` | Выбор видеокарт | `--devices 0,1` |
| `--help` | Справка по параметрам | `./nacreon --help` |
| `--version` | Версия майнера | `./nacreon --version` |

Для выбора отдельных карт добавьте `--devices 0,1` к команде запуска.
Остановка — **Ctrl+C**. Дождитесь завершения перед повторным запуском.
Майнер сохраняет текущие настройки разгона.

## HiveOS

В Flight Sheet выберите **Custom miner** и укажите:

| Поле | Значение |
|---|---|
| Miner name | `Nacreon_hiveos_custom` |
| Installation URL | Ссылка на `Nacreon_hiveos_custom-0.23.0.tar.gz` из официального релиза |
| Algorithm | `pearlhash` |
| Wallet / worker template | `%WAL%.%WORKER_NAME%` |
| Pool URL | `prl.kryptex.network:7048` |
| Password | `x` |
| Extra config | Пусто либо `--devices 0,1` для выбора карт |

Используйте свой Pearl-кошелёк. Запуск, остановка и разгон — через HiveOS.

## Обновление

Остановите майнер. Сохраните предыдущую версию и свои настройки.
Распакуйте новый архив в отдельный каталог; не смешивайте файлы разных версий.
Для возврата остановите новую версию и запустите предыдущую.

## Лицензии

Условия использования — `LICENSE.txt`. Сторонние лицензии и уведомления — в `licenses/`.
