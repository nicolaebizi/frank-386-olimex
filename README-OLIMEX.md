# FRANK-386 pentru Olimex RP2350PC (Rev.A1) / FRANK-386 for Olimex RP2350PC (Rev.A1)

Port al **frank-386 v1.08** (emulator PC i386 pe RP2350, Tiny386/FRANK) pe placa
**Olimex RP2350PC** — HW-validat: bootează **SeaBIOS → DR-DOS 8.1** de pe disketa
`fdd0.img`, tastatură USB funcțională (DIR răspunde).

A port of **frank-386 v1.08** (i386 PC emulator on RP2350, Tiny386/FRANK) to the
**Olimex RP2350PC** board — hardware-validated: boots **SeaBIOS → DR-DOS 8.1**
from the `fdd0.img` floppy image, working USB keyboard (DIR responds).

---

## Configurare hardware / Hardware configuration

| Subsistem | Detalii |
|---|---|
| MCU | RP2350B (QFN-80), flash 16 MB, **CPU 378 MHz** / **PSRAM 133 MHz** |
| HDMI | PIO **`out pins, 8`**, fără side-set — ordine neregulată de lane-uri: **D0=12/13, CK=14/15 (în câmp, biții 2/3), D2=16/17, D1=18/19** |
| HDMI conv | adaptare la **336 indici**: `conv in x,18` / `0x4032`, intrare în tabel 32 B (5 din 8 cuvinte), index shiftat 5, X = base>>14, **bloc 16 KB aliniat 16 KB** |
| HDMI polaritate | **`invert_diffpairs = 0`** — fix culori (XOR-ul suplimentar producea culori complementare pe RP2350PC) |
| PSRAM | 8 MB APS6404L pe QMI **CS1 = GPIO8** (+ fallback runtime din upstream) |
| SD | **spi1**: CS=9, SCK=10, MOSI=11, MISO=24 |
| Tastatură | hub USB integrat 4×A — TinyUSB **host**; PS/2 compilat out în build-urile HID (GPIO0/1 = UEXT1) |
| Consolă UART | **GP0 @ 115200** activat în build-urile PC+USB_HID (capcană: upstream are stdio OFF pentru PC — noi îl activăm) |
| Audio | **PWM GP26 (L) / GP27 (R)** → filtru RC; I2S+ES8311 = viitor |

## Build

```bash
cmake -DPICO_SDK_PATH=/path/to/pico-sdk \
      -DBOARD=PC \
      -DCPU_SPEED=378 -DPSRAM_SPEED=133 \
      -DUSB_HID_ENABLED=ON \
      -DDEBUG_ENABLED=ON \
      -DCMAKE_BUILD_TYPE=Release \
      -G Ninja -B build-pc-3 -S .
cmake --build build-pc-3 --parallel 6
```

Rezultat: `build-pc-3/PCp2-386-378MHz-P133-v1.08.uf2`.

`386/config.ini` din repo este deja corectat: **378/133** (NU 504 — nevalidat pe
placa aceasta; `config.ini` a fost editat și pe cardul SD de lângă firmware).

## DATELE: disketa DR-DOS — NU este inclusă (©)

**Română:** `fdd0.img` (DR-DOS 8.1) este **software comercial protejat de
copyright și NU este inclus** în acest repo. Descarcă-l din repo-ul upstream
[github.com/rh1tech/frank-386](https://github.com/rh1tech/frank-386) (folder
`386/`) și copiază-l pe cardul SD formatat FAT32, alături de `config.ini`.

`bios.bin` / `vgabios.bin` din folderul `386/` sunt **SeaBIOS** (LGPL v3) —
redistribuibile; vezi [seabios.org](https://www.seabios.org/) și nota de
licență din `LICENSE`.

**English:** `fdd0.img` (DR-DOS 8.1) is **commercial copyrighted software and
NOT included** in this repository. Download it from the upstream repository
[github.com/rh1tech/frank-386](https://github.com/rh1tech/frank-386) (folder
`386/`) and copy it to a FAT32-formatted SD card together with `config.ini`.
`bios.bin` / `vgabios.bin` are **SeaBIOS** (LGPL v3) — redistributable; see
[seabios.org](https://www.seabios.org/).

## Flash

1. Ține apăsat **BOOT1** + apasă **RESET** → apare unitatea **RPI-RP2**.
2. Copiază `PCp2-386-378MHz-P133-v1.08.uf2` (din `build-pc-3/`, sau din Release).
3. Placa repornește: HDMI 640×480@60, SeaBIOS → DR-DOS de pe disketa `fdd0.img`.

## Controale

- **Win+F11** — setări
- **Win+F12** — disk manager
- Tastatura este USB (hub-ul integrat al plăcii); consola de debug pe UART
  GP0 @ 115200 (util pentru `DEBUG_ENABLED`).

## Credits

- **frank-386 / FRANK (Murmulator)** — Mikhail Matveev (rh1tech)
- **Tiny386** — Chunhui He (DnCraptor), BSD
- **Pico-286** — xrip, MIT
- **QEMU** — acceleratorul i386 portat
- **MAME fmopl** — LGPL
- **SeaBIOS** — LGPL v3, [seabios.org](https://www.seabios.org/)
- **FatFs** — ChaN, BSD

Licențe: vezi `LICENSE` și sursele respective. Portarea Olimex se respectă
aceleași licențe ca proiectul de bază (v1.08 upstream, vezi
[github.com/rh1tech/frank-386](https://github.com/rh1tech/frank-386)).
