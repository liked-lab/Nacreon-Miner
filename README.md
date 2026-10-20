# Nacreon Miner 0.23.2 · RTX 50 test

Pearl (PRL / PearlHash) · NVIDIA GeForce RTX 50 · Linux / HiveOS

**Developer / Разработчик: liked · DevFee: 0.5% / 0,5%.** Pool fees are separate / Комиссия пула отдельно.

[Русский](#русский) · [English](#english) · [Downloads / Скачать](https://github.com/liked-lab/Nacreon-Miner/releases/tag/v0.23.2)

## Русский

Тестовое обновление для RTX 50. Исправлены имя воркера, переключение серверов комиссии, состояние связи и отображение статистики. В консоли — счётчик подтверждённых сбоев GPU/драйвера и три последние принятые пользовательские шары. Обновлён учёт хешрейта; изменение показаний само по себе не означает ускорение. Прирост скорости не заявляется.

### Linux: установка и запуск

Нужны 64-разрядный Linux и совместимый драйвер NVIDIA для RTX 50. Скачайте `Nacreon_linux-0.23.2.tar.gz` из релиза. [Проверка подписи и SHA-256](https://github.com/liked-lab/Nacreon-Miner/blob/main/SIGNATURES.md).

```bash
tar -xzf Nacreon_linux-0.23.2.tar.gz
cd nacreon
./nacreon --pool prl.kryptex.network:7048 --wallet YOUR_PEARL_WALLET --worker rig01 --password x
```

Замените `YOUR_PEARL_WALLET` на **свой** адрес Pearl либо логин аккаунта, который требует ваш пул. `rig01` — **ваше** имя воркера. Не вводите закрытый ключ или seed-фразу. Для имени воркера используйте латинские буквы, цифры, `_` и `-` (до 63 символов). Пароль `x` подходит, если пул не требует другой.

Для Kryptex кошелёк и имя воркера можно также передать вместе:

```bash
./nacreon --pool prl.kryptex.network:7048 --wallet YOUR_PEARL_WALLET.rig01
```

Используйте один способ задания имени: отдельный `--worker` либо суффикс `.rig01`. Основная работа идёт на указанный пользователем кошелёк. Комиссия разработчика учитывается отдельно, имя её воркера — `nacreon-devfee`.

### Основные параметры

| Параметр | Что задаёт | Пример |
|---|---|---|
| `--pool`, `-o` | Пул и порт; можно повторять для резервных подключений | `--pool prl.kryptex.network:7048` |
| `--wallet`, `-w` | Ваш кошелёк или логин пула | `--wallet YOUR_PEARL_WALLET` |
| `--worker`, `-r` | Ваше имя воркера | `--worker rig01` |
| `--password`, `-p` | Пароль пула, по умолчанию `x` | `--password x` |
| `--devices`, `-d` | Выбранные CUDA-номера или GPU UUID; без параметра — автоматически | `--devices 0,1` |
| `--coff` | Смещение ядра, МГц | `--coff 100` |
| `--cclk` | Фиксированная частота ядра, МГц | `--cclk 2400` |
| `--moff` | Смещение памяти, МГц драйвера | `--moff 100` |
| `--mclk` | Фиксированная частота памяти, МГц драйвера | `--mclk 810` |
| `--pl` | Power limit **каждой карты**, Вт | `--pl 180` |
| `--fan` | Вентиляторы: процент либо автоматический режим | `--fan 60` / `--fan auto` |
| `--help` | Справка | `./nacreon --help` |
| `--version` | Версия | `./nacreon --version` |

Остановка — **Ctrl+C**. Дождитесь завершения перед повторным запуском.

### Linux: power limit, ядро и память

Для изменения настроек GPU нужны права **root** (`sudo`) и поддержка драйвера/карты. Без параметров разгона майнер оставляет текущие настройки. Числа ниже — примеры синтаксиса, а не универсальный профиль RTX 50: подбирайте допустимые значения для своей карты.

Лимит 180 Вт на каждую выбранную карту:

```bash
sudo ./nacreon --pool prl.kryptex.network:7048 --wallet YOUR_PEARL_WALLET --worker rig01 --devices 0,1 --pl 180
```

Смещение ядра +100 МГц, смещение памяти +100 МГц и лимит мощности:

```bash
sudo ./nacreon --pool prl.kryptex.network:7048 --wallet YOUR_PEARL_WALLET --worker rig01 --coff 100 --moff 100 --pl 180
```

Фиксированная частота памяти задаётся **вместо её смещения**. Например, если карта и драйвер поддерживают 810 МГц:

```bash
sudo ./nacreon --pool prl.kryptex.network:7048 --wallet YOUR_PEARL_WALLET --worker rig01 --coff 100 --mclk 810 --pl 180
```

`--coff 100` означает **+100 к ядру**, а `--cclk 2400` — **задать ядро 2400 МГц**. Аналогично `--moff` — смещение памяти, `--mclk` — фиксированная частота. Значения памяти указаны в единицах драйвера, их нельзя автоматически приравнивать к эффективной частоте памяти или числам из другого ПО. Недопустимые или неподдерживаемые настройки приводят к сообщению об ошибке; это не подтверждение, что настройка применена.

Одно значение действует на все выбранные GPU. Список через запятую соответствует порядку `--devices`; `*` оставляет настройку этой карты без изменений. Пример: GPU 0 — 180 Вт / +100 МГц ядро; GPU 1 — 190 Вт / +125 МГц:

```bash
sudo ./nacreon --pool prl.kryptex.network:7048 --wallet YOUR_PEARL_WALLET --worker rig01 --devices 0,1 --pl 180,190 --coff 100,125 --fan 60,auto
```

Чтобы оставить смещение GPU 1 прежним: `--coff '100,*'`. Не задавайте противоречащие настройки одновременно в нескольких программах. Проверяйте температуру, принятые/отклонённые шары и стабильность после изменения разгона.

### Резервный пул

```bash
./nacreon --pool prl.kryptex.network:7048 --pool prl-ru.kryptex.network:7048 --wallet YOUR_PEARL_WALLET --worker rig01
```

Повторный `--pool` добавляет резерв подключения, а не второй одновременно работающий пул. Для пользовательского майнинга выбираются указанные вами адреса. Для комиссии разработчика предусмотрено отдельное автоматическое переключение региональных серверов.

### HiveOS

Создайте кошелёк со **своим адресом**, выберите его в полётном листе и настройте **Custom miner**:

| Поле | Значение |
|---|---|
| Miner name | `Nacreon_hiveos_custom` |
| Installation URL | `https://github.com/liked-lab/Nacreon-Miner/releases/download/v0.23.2/Nacreon_hiveos_custom-0.23.2.tar.gz` |
| Algorithm | `pearlhash` |
| Wallet / worker template | `%WAL%.%WORKER_NAME%` |
| Pool URL | `prl.kryptex.network:7048` |
| Password | `x` |
| Extra config | Пусто либо, например, `--devices 0,1` |

`%WAL%` подставляет выбранный кошелёк, `%WORKER_NAME%` — имя вашего воркера HiveOS. Введите шаблон целиком. Разгон и power limit удобно задавать штатно в HiveOS. Не дублируйте их противоречащими значениями в Extra config. Консоль — команда `miner`; запуск и остановка — средствами HiveOS.

### Экран и обновление

Экран обновляется раз в 5 секунд. «Сеанс связи» — длительность текущего подключения к пулу. «Сбои» — подтверждённые события GPU/драйвера за запуск: `0` при полном наблюдении без событий, число при событиях, `—` при недостаточных данных. Отклонённые пулом шары учитываются отдельно. Это не диагностика исправности оборудования. Внизу — три последние принятые пользовательские шары и время ответа пула (ACK). Для сырых диагностических сообщений: `NACREON_RAW_CONSOLE=1 ./nacreon ...`.

Перед обновлением остановите майнер, сохраните прежнюю версию и свои настройки. Распакуйте новую версию в отдельный каталог. Для возврата остановите её и запустите прежнюю. Условия использования — `LICENSE.txt`, сторонние лицензии — `licenses/`.

## English

Test update for RTX 50. Fixes worker naming, regional DevFee failover, connection status and statistics. The console includes confirmed GPU/driver fault counts and the last three accepted user shares. Hashrate accounting has changed; a different displayed number does not itself mean faster mining. No performance increase is claimed.

### Linux: install and run

Requires 64-bit Linux and an NVIDIA driver compatible with RTX 50. Download `Nacreon_linux-0.23.2.tar.gz` from the release. [Verify signatures and SHA-256](https://github.com/liked-lab/Nacreon-Miner/blob/main/SIGNATURES.md).

```bash
tar -xzf Nacreon_linux-0.23.2.tar.gz
cd nacreon
./nacreon --pool prl.kryptex.network:7048 --wallet YOUR_PEARL_WALLET --worker rig01 --password x
```

Replace `YOUR_PEARL_WALLET` with **your own** Pearl address or the account login required by your pool. Replace `rig01` with **your own** worker name. Never enter a private key or seed phrase. Worker names accept Latin letters, digits, `_` and `-`, up to 63 characters. The default pool password is `x`; use a different one if required by your pool.

For Kryptex, the combined form is also supported:

```bash
./nacreon --pool prl.kryptex.network:7048 --wallet YOUR_PEARL_WALLET.rig01
```

Use either a separate `--worker` or the `.rig01` suffix. Main mining uses the wallet supplied by the user. The **0.5% developer fee** is accounted for separately under worker name `nacreon-devfee`. Pool fees are separate.

### Main options

| Option | Purpose | Example |
|---|---|---|
| `--pool`, `-o` | Pool host and port; repeat for connection failover | `--pool prl.kryptex.network:7048` |
| `--wallet`, `-w` | Your wallet or pool account login | `--wallet YOUR_PEARL_WALLET` |
| `--worker`, `-r` | Your worker name | `--worker rig01` |
| `--password`, `-p` | Pool password; default `x` | `--password x` |
| `--devices`, `-d` | CUDA ordinals or GPU UUIDs; automatic selection by default | `--devices 0,1` |
| `--coff` | Core clock offset, MHz | `--coff 100` |
| `--cclk` | Locked core clock, MHz | `--cclk 2400` |
| `--moff` | Memory clock offset, driver MHz | `--moff 100` |
| `--mclk` | Locked memory clock, driver MHz | `--mclk 810` |
| `--pl` | Power limit **per GPU**, watts | `--pl 180` |
| `--fan` | Fan percentage or automatic control | `--fan 60` / `--fan auto` |
| `--help` | Command help | `./nacreon --help` |
| `--version` | Miner version | `./nacreon --version` |

Stop with **Ctrl+C** and wait for exit before starting again.

### Linux: power limit, core and memory settings

GPU tuning requires **root** (`sudo`) and support from your GPU/driver. Without tuning options, existing GPU settings are preserved. The numbers below demonstrate syntax; they are not a universal RTX 50 profile. Choose values supported by your own cards.

Set a 180 W limit for each selected GPU:

```bash
sudo ./nacreon --pool prl.kryptex.network:7048 --wallet YOUR_PEARL_WALLET --worker rig01 --devices 0,1 --pl 180
```

Apply a +100 MHz core offset, +100 driver MHz memory offset and a power limit:

```bash
sudo ./nacreon --pool prl.kryptex.network:7048 --wallet YOUR_PEARL_WALLET --worker rig01 --coff 100 --moff 100 --pl 180
```

Use a memory lock **instead of a memory offset**. For example, if your card and driver support 810 MHz:

```bash
sudo ./nacreon --pool prl.kryptex.network:7048 --wallet YOUR_PEARL_WALLET --worker rig01 --coff 100 --mclk 810 --pl 180
```

`--coff 100` adds **100 MHz to the core clock**; `--cclk 2400` requests a **2400 MHz core lock**. Likewise, `--moff` is a memory offset and `--mclk` is a memory lock. Memory settings use driver MHz: do not assume they match effective memory rates or values shown by other software. Unsupported or out-of-range settings produce an error; an error does not mean the requested setting was applied.

One value applies to all selected GPUs. Comma-separated values follow `--devices` order; `*` preserves that GPU's setting. Example: GPU 0 at 180 W / +100 MHz core, GPU 1 at 190 W / +125 MHz core:

```bash
sudo ./nacreon --pool prl.kryptex.network:7048 --wallet YOUR_PEARL_WALLET --worker rig01 --devices 0,1 --pl 180,190 --coff 100,125 --fan 60,auto
```

Use `--coff '100,*'` to preserve GPU 1's core offset. Avoid conflicting settings from multiple tools. Check temperatures, accepted/rejected shares and stability after changing clocks.

### Backup pool

```bash
./nacreon --pool prl.kryptex.network:7048 --pool prl-ru.kryptex.network:7048 --wallet YOUR_PEARL_WALLET --worker rig01
```

Repeating `--pool` adds connection failover, not a second pool mining simultaneously. User mining uses the pool addresses you supply. Developer mining has its own automatic regional failover.

### HiveOS

Create a wallet with **your own address**, select it in the Flight Sheet and configure **Custom miner**:

| Field | Value |
|---|---|
| Miner name | `Nacreon_hiveos_custom` |
| Installation URL | `https://github.com/liked-lab/Nacreon-Miner/releases/download/v0.23.2/Nacreon_hiveos_custom-0.23.2.tar.gz` |
| Algorithm | `pearlhash` |
| Wallet / worker template | `%WAL%.%WORKER_NAME%` |
| Pool URL | `prl.kryptex.network:7048` |
| Password | `x` |
| Extra config | Empty, or e.g. `--devices 0,1` |

`%WAL%` expands to your selected wallet; `%WORKER_NAME%` expands to your HiveOS worker name. Enter the complete template. Use HiveOS overclocking controls for clocks and power limits; avoid conflicting values in Extra config. Open the console with `miner`; start and stop through HiveOS.

### Display and updates

The screen refreshes every 5 seconds. Connection session shows the duration of the current pool connection. Faults count confirmed GPU/driver events during this run: `0` means complete observation with no events, a number means observed events, and `—` means insufficient data. Pool rejections are separate. This is not a hardware health diagnosis. The footer shows the last three accepted user shares and pool response latency (ACK). For raw diagnostic output: `NACREON_RAW_CONSOLE=1 ./nacreon ...`.

Before updating, stop the miner and keep the previous version and your settings. Extract the update into a separate directory. To roll back, stop it and start the previous version. Terms: `LICENSE.txt`; third-party notices: `licenses/`.
