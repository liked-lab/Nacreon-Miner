# Nacreon Miner

**Developed by liked.** Experimental Pearl (PRL / pearlhash) miner for Linux x86_64 and HiveOS. Developer fee: **0%**. Pool fees are separate.

## Release V22 / 0.22.0-experimental

The HiveOS package was repaired on 8 October 2026. Use miner name **`nacreon-0.22.0`**. Its archive root and manifest now match HiveOS custom-get. Empty extra arguments select all NVIDIA GPUs in PCI order; `--devices 0,1` selects only those cards.

The repaired HiveOS executable keeps the exact V22 algorithm and includes native SM75/86/89 plus PTX for driver JIT on newer GPUs. Physical validation of this repair covers seven RTX 5070 cards; all seven passed exact CPU/GPU checks, offline proof verification passed, and a 300-second Kryptex test yielded 9 accepted / 0 rejected shares with 9 locally verified proofs. This does not establish support for every GPU or HiveOS image. The original standalone Linux package remains SM89-only.

### Original V22 validation

This binary-only release packages our best verified V22 implementation. Its compute ELF is unchanged from the validated checkpoint, SHA256 `7e1686443e93297a45b893995d2979b2254f099564b2c10ef91520fe282d8154`. The Nacreon Miner launcher sets the exact tested V22 environment. The underlying executable retains its original internal program name; branding is provided by the launcher and HiveOS integration.

The original standalone Linux binary targets **NVIDIA Ada / SM89**. Physical validation covers **RTX 4080 and RTX 4070 Ti SUPER only**. RTX 20/30/50 series, other GPUs and Windows are not supported by the original standalone Linux package. See the repaired HiveOS package above for RTX 5070.

Requirements: Linux x86_64 with glibc 2.35 or newer, compatible NVIDIA driver for CUDA 12.6, libstdc++ and libgomp. HiveOS integration also requires Bash, Python 3 and jq. CUDA runtime is linked statically; no CUDA Toolkit installation is required. Compatibility with every HiveOS image has not been established.

## Linux

Download the `.hiveos.tar.gz` asset from Releases; the same archive includes a standalone Linux launcher. Check its SHA256 against `SHA256SUMS`, then:

```bash
tar -xzf nacreon-0.22.0-experimental.hiveos.tar.gz
cd nacreon-0.22.0
./nacreon --version
./nacreon --backend cuda --devices 0,1 \
  --pool stratum+tcp://prl.kryptex.network:7048 \
  --wallet YOUR_PRL_WALLET --worker YOUR_WORKER --verify
```

Replace the wallet and worker placeholders with your own settings. Adjust `--devices` to your GPU indices. The package contains no default mining wallet.

## HiveOS custom miner

Select **Custom** miner in a PRL flight sheet. Set miner name to `nacreon-0.22.0` and installation URL to this release's `.hiveos.tar.gz` asset. Set wallet template to your existing `WALLET.WORKER`, pool URL to `prl.kryptex.network:7048` and leave extra arguments empty for all NVIDIA GPUs, or set `--devices 0,1`. Integration reads your flight-sheet settings; it never changes GPU clocks, memory clocks, voltage or power limits.

HiveOS displays an aggregate local job-wall rate, not invented per-GPU rates. Its rate is not an independent pool hashrate estimate.

## Validation and limits

- Exact CUDA/CPU oracle and mock-proof checks passed in the V22 experiment.
- Two offline full-sweep runs including preparation: 316.53 and 316.61 TMAC/s on the tested two-GPU rig.
- A 300-second Kryptex run with local proof verification: 11 accepted shares, 0 rejected; median local job-wall rate 308.01 TMAC/s.
- Recorded test clocks: core 2400 MHz, memory 5001 MHz; historical power limits 300 / 240 W. These are test conditions, not settings applied by the miner.
- These short tests do not establish long-term stability, pool throughput or superiority over SRBMiner. Different miners' local metrics may measure different work scopes.

## Attribution and distribution

Based on MIT-licensed CPPminer by foolzhz. Original copyright and dependency notices are included in `LICENSE`, `NOTICE.txt` and `licenses/`. Only binaries, integration scripts and documentation are published; source for liked's modifications remains private. This package contains no SRBMiner or KRig executable or code.

## Русский

Разработчик — **liked**. Комиссия разработчика **0%**, комиссия пула отдельно.
HiveOS-пакет исправлен 8 октября 2026: имя майнера `nacreon-0.22.0`, все NVIDIA-карты выбираются автоматически. Сборка V22 содержит PTX для новых NVIDIA; проверены семь RTX 5070. Исходники изменений закрыты, лицензии сохранены. Майнер не меняет разгон, напряжение и лимиты питания. Исходный отдельный Linux-пакет остаётся SM89. Превосходство над SRBMiner не установлено.
