# FRANK-386 Olimex RP2350PC — Firmware v1.08-olimex

Firmware HW-validat pentru placa **Olimex RP2350PC (Rev.A1)**: bootează
**SeaBIOS → DR-DOS 8.1** de pe disketa `fdd0.img`, tastatură USB funcțională.

## Fișier

`frank-386-olimex.uf2` = `PCp2-386-378MHz-P133-v1.08.uf2` (build-pc-3:
consolă UART + rework HDMI)

SHA-256:
`fb2a94c464247a115db6b5635d9a798d191025acc36324101cf8e5517bbce019`

## Config placă

- HDMI: PIO `out pins, 8`, lane-uri **D0=12/13, CK=14/15, D2=16/17, D1=18/19**;
  conv la **336 indici** (`in x,18` / `0x4032`, bloc **16 KB** aliniat 16 KB);
  `invert_diffpairs=0` (fix culori)
- PSRAM: QMI CS1=**GP8**
- SD: **spi1** (9/10/11/24)
- Tastatură: hub USB integrat; PS/2 off în build-urile HID
- Audio: **PWM GP26 (L) / GP27 (R)**; I2S+ES8311 = viitor
- Consolă debug: **UART GP0 @ 115200** (PC+USB_HID)

## Necesar pe cardul SD (FAT32)

- `fdd0.img` (DR-DOS 8.1) — **NU inclus** (© comercial): descarcă din
  [rh1tech/frank-386](https://github.com/rh1tech/frank-386) (folder `386/`)
- `config.ini` din acest repo — **378/133** (NU 504, nevalidat pe placă)

## Flash

BOOT1 + RESET → **RPI-RP2** → copiază `frank-386-olimex.uf2`.

## Controale

- **Win+F11** — settings
- **Win+F12** — disk manager
