# Nacreon 0.23.3-three-family-rc2

Pearl (PRL / PearlHash) · NVIDIA RTX30 / RTX40 / RTX50 · Linux / HiveOS · USER-only

Русский: тестовый релиз; текущие ELF проверены на RTX30/40/50, ограничения описаны ниже.
English: test release; current ELF files received physical RTX30/40/50 checks; scope and limits are listed below.
Python3.10+, Linux x86-64 and a compatible NVIDIA driver are required.
For a homogeneous CUDA-visible rig the launcher selects SM86/SM89/SM120.
For a mixed rig set NACREON_GPU_ARCH=sm86|sm89|sm120 and matching --devices.
No architecture selection logic or console layout has changed.

## Поддерживаемые модели / Supported models

Комиссия Nacreon / Nacreon developer fee: **0%**. Комиссия пула определяется пулом / Pool fees are separate.

| Архитектура / Architecture | GeForce RTX — совместимость по архитектуре / architecture compatibility |
|---|---|
| RTX30 / SM86 (CUDA8.6) | RTX3050,3060,3060Ti,3070,3070Ti,3080,3080Ti,3090,3090Ti |
| RTX40 / SM89 (CUDA8.9) | RTX4060,4060Ti,4070,4070SUPER,4070Ti,4070TiSUPER,4080,4080SUPER,4090 |
| RTX50 / SM120 (CUDA12.0) | RTX5050,5060,5060Ti,5070,5070Ti,5080,5090 |

Это совместимость сборки по CUDA-архитектуре, а не физический тест каждого SKU. Физически проверены пять RTX3070/RTX3070Ti, одна RTX4070Ti и две RTX5070. / Architecture compatibility is distinct from physical qualification of every SKU: five RTX3070/RTX3070Ti cards, one RTX4070Ti and two RTX5070 were physically tested.

Источник моделей и архитектур / Model and architecture references: [NVIDIA CUDA GPU list](https://developer.nvidia.com/cuda/gpus), [NVIDIA RTX40 comparison](https://www.nvidia.com/en-gb/geforce/graphics-cards/compare/?section=compare-40). The SUPER mapping follows NVIDIA's Ada architecture classification and the SM89 selector contract.

## Русский

Тестовый релиз Nacreon для RTX30/SM86, RTX40/SM89 и RTX50/SM120. Текущие бинарники прошли короткие GPU-проверки; это не проверка каждого SKU и не длительный soak.

### Linux: установка и запуск

Нужны x86-64 Linux, glibc 2.34+, libssl/libcrypto 3, совместимый драйвер NVIDIA RTX 30, writable tmpfs /run либо /dev/shm; Python 3.10+ для консоли/Hive. Скачайте Linux-архив из Assets этого релиза и проверьте SHA256SUMS и Sigstore-подпись.

```bash
tar -xzf Nacreon_linux-0.23.3-three-family-rc2.tar.gz
cd nacreon
./nacreon --pool prl.kryptex.network:7048 --wallet YOUR_PEARL_WALLET --worker rig01 --password x
```

Замените `YOUR_PEARL_WALLET` на **свой** адрес Pearl либо логин аккаунта, который требует ваш пул. `rig01` — **ваше** имя воркера. Не вводите закрытый ключ или seed-фразу. Для имени воркера используйте латинские буквы, цифры, `_` и `-` (до 63 символов). Пароль `x` подходит, если пул не требует другой.

Для Kryptex кошелёк и имя воркера можно также передать вместе:

```bash
./nacreon --pool prl.kryptex.network:7048 --wallet YOUR_PEARL_WALLET.rig01
```

Используйте отдельный --worker либо суффикс .rig01. Вся выделяемая этой обвязкой работа относится к USER.

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

Для изменения настроек GPU нужны права **root** (`sudo`) и поддержка драйвера/карты. Без параметров разгона майнер оставляет текущие настройки. Числа ниже — примеры синтаксиса, а не универсальный профиль RTX 30: подбирайте допустимые значения для своей карты.

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

Повторный `--pool` добавляет резерв подключения, а не второй одновременно работающий пул. Для пользовательского майнинга выбираются указанные вами адреса.

### HiveOS

Создайте кошелёк со **своим адресом**, выберите его в полётном листе и настройте **Custom miner**:

| Поле | Значение |
|---|---|
| Miner name | `Nacreon_hiveos_custom` |
| Installation URL | https://github.com/liked-lab/Nacreon-Miner/releases/download/v0.23.3-three-family-rc2/Nacreon_hiveos_custom-0.23.3-three-family-rc2.tar.gz |
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

Nacreon test release for RTX30/SM86, RTX40/SM89 and RTX50/SM120. Current binaries passed short physical mining checks, not every-SKU or long-term stability qualification.

### Linux: install and run

Requires x86-64 Linux, glibc 2.34+, libssl/libcrypto 3, a compatible NVIDIA RTX 30 driver, writable tmpfs /run or /dev/shm, and Python 3.10+ for console/Hive. Download the Linux archive from this release Assets and verify SHA256SUMS and its Sigstore bundle.

```bash
tar -xzf Nacreon_linux-0.23.3-three-family-rc2.tar.gz
cd nacreon
./nacreon --pool prl.kryptex.network:7048 --wallet YOUR_PEARL_WALLET --worker rig01 --password x
```

Replace `YOUR_PEARL_WALLET` with **your own** Pearl address or the account login required by your pool. Replace `rig01` with **your own** worker name. Never enter a private key or seed phrase. Worker names accept Latin letters, digits, `_` and `-`, up to 63 characters. The default pool password is `x`; use a different one if required by your pool.

For Kryptex, the combined form is also supported:

```bash
./nacreon --pool prl.kryptex.network:7048 --wallet YOUR_PEARL_WALLET.rig01
```

Use either --worker or a .rig01 suffix. This host allocates work only to USER.

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

GPU tuning requires **root** (`sudo`) and support from your GPU/driver. Without tuning options, existing GPU settings are preserved. The numbers below demonstrate syntax; they are not a universal RTX 30 profile. Choose values supported by your own cards.

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

Repeating `--pool` adds connection failover, not a second pool mining simultaneously. User mining uses the pool addresses you supply.

### HiveOS

Create a wallet with **your own address**, select it in the Flight Sheet and configure **Custom miner**:

| Field | Value |
|---|---|
| Miner name | `Nacreon_hiveos_custom` |
| Installation URL | https://github.com/liked-lab/Nacreon-Miner/releases/download/v0.23.3-three-family-rc2/Nacreon_hiveos_custom-0.23.3-three-family-rc2.tar.gz |
| Algorithm | `pearlhash` |
| Wallet / worker template | `%WAL%.%WORKER_NAME%` |
| Pool URL | `prl.kryptex.network:7048` |
| Password | `x` |
| Extra config | Empty, or e.g. `--devices 0,1` |

`%WAL%` expands to your selected wallet; `%WORKER_NAME%` expands to your HiveOS worker name. Enter the complete template. Use HiveOS overclocking controls for clocks and power limits; avoid conflicting values in Extra config. Open the console with `miner`; start and stop through HiveOS.

### Display and updates

The screen refreshes every 5 seconds. Connection session shows the duration of the current pool connection. Faults count confirmed GPU/driver events during this run: `0` means complete observation with no events, a number means observed events, and `—` means insufficient data. Pool rejections are separate. This is not a hardware health diagnosis. The footer shows the last three accepted user shares and pool response latency (ACK). For raw diagnostic output: `NACREON_RAW_CONSOLE=1 ./nacreon ...`.

Before updating, stop the miner and keep the previous version and your settings. Extract the update into a separate directory. To roll back, stop it and start the previous version. Terms: `LICENSE.txt`; third-party notices: `licenses/`.

RU: Запуск самостоятелен. Внешние compute-модули не являются собственными ядрами Nacreon; см. COMPUTE_NOTICE.md. Настройки GPU меняются только при явных параметрах.

EN: Standalone runtime. External compute modules are not Nacreon-authored kernels; see COMPUTE_NOTICE.md. GPU tuning is applied only through explicit options.

## Квалификация / Qualification

Текущие бинарники: около500с реального майнинга; RTX30/40/50 —8/6/6 принятых USER-шар без отказов. Полные prepool-проверки fullK/zero/4096 PASS. Возврат штатных майнеров проверен отдельными окнами не менее240с с новыми ACK. RTX30 mining и адресный возврат проверены отдельными фазами. Поздний FAIL лабораторного guard на RTX50 сохранён; последующие независимые проверки возврата и очистки PASS. Это короткая квалификация, не длительный тест стабильности и не гарантия ускорения.

Current binaries received approximately500s physical mining checks:8/6/6 accepted USER shares on RTX30/40/50 without rejected shares, full prepool math checks, and original-miner recovery verification. RTX30 mining and targeted recovery were separate phases. A late RTX50 laboratory guard failure remains disclosed; subsequent independent return and cleanup checks passed. These are short checks, not an every-SKU qualification, long soak or speedup guarantee.

Один процесс использует одну архитектуру; для смешанного рига выбирайте архитектуру и соответствующие --devices отдельно. / One process uses one architecture; select matching architecture/devices separately for mixed rigs.

Архивы сохранены побайтно после сборки: пометки private/UNRUN внутри упакованного README отражают этап до GPU-проверок. Актуальные результаты и список моделей приведены здесь и в README репозитория. / Archives remain byte-identical to their qualified builds; private/UNRUN wording inside the packaged README records the pre-GPU build stage. This release page and repository README contain the current qualification status and model list.

USER-only: комиссия Nacreon0%. Комиссия пула отдельная. Короткие тесты не доказывают отсутствие сторонних комиссий во всех возможных режимах. / Nacreon developer fee0%; pool fees are separate. Short tests do not establish absence of every third-party fee in every possible mode.

Проверьте SHA256SUMS и Sigstore-подписи перед установкой. / Verify SHA256SUMS and Sigstore bundles before installation. Required notices remain included.
