---
title: "Qingping Air Monitor Lite (CGDN1)"
date-published: 2026-10-18
type: sensor
standard: global
board: esp32
project-url: https://github.com/nemethhh/esphome-qingping-cgdn1
difficulty: 2
---

<!-- TODO: add a photo next to this file and reference it here, e.g.
![Qingping Air Monitor Lite](qingping-air-monitor-lite.jpg "Qingping Air Monitor Lite (CGDN1)") -->

## Description

The Qingping Air Monitor Lite (CGDN1) measures CO2 (Sensirion SCD40), PM1.0/2.5/10 (Plantower PMSA003I) and
temperature/humidity (Sensirion SHT4x). It has a 128 × 160 OLED, a capacitive touch slider, an RGB status LED, a back
power button and a battery. An ESP32 (ESP32-WROVER-B, "Dany-ESP" hardware, stock firmware 4.8.x) drives all of it
directly.

The board-specific parts are external components from the
[project repository](https://github.com/nemethhh/esphome-qingping-cgdn1):

- `cgdn1_boot` holds the power latch from the earliest init (without it the unit switches itself off), supplies the
  stock partition table and handles the long-press power-off.
- `cgdn1_display` drives the OLED and detects which of the two factory panel variants is fitted, like stock.
- `cgdn1_touch` reads the ATtiny814 touch slider and decodes taps and swipes.

The [full configuration](https://github.com/nemethhh/esphome-qingping-cgdn1/blob/v0.2.0/cgdn1.yaml) in the project
repository also re-creates the stock screen pages, the stock LED colours and night mode, and adds a passive Bluetooth
proxy and optional on-screen messages over MQTT. It compensates the board's self-heating of temperature and humidity,
either like stock or with a measured display/Bluetooth heat model tuned from Home Assistant. Without compensation the
SHT4x reads about 6 °C high once the board is warm.

## Installation

The UART is only reachable inside the case, behind a glued-in screen, so ESPHome is installed over the air through the
stock firmware. With the monitor connected to a local MQTT broker, the stock OTA can be told to download an image from
your PC. The project's [install guide](https://github.com/nemethhh/esphome-qingping-cgdn1/blob/v0.2.0/docs/install.md)
covers the whole procedure, including a reversible trial mode that returns to stock on every reboot.

There is no rollback: an image that fails to boot can only be recovered over the UART, which means removing the
glued-in screen.

Keep the stock partition table: `cgdn1_boot` supplies it, so don't set `partitions:`. Push only the app image
(`firmware.ota.bin`), never `firmware.factory.bin`.

## GPIO Pinout

| Pin    | Function                                             |
| ------ | ---------------------------------------------------- |
| GPIO23 | Power-hold latch (must be HIGH at boot)              |
| GPIO13 | Sensor rail enable                                   |
| GPIO18 | Sensor rail enable                                   |
| GPIO26 | I2C bus 0 SDA: SCD40 0x62, PMSA003I 0x12, SHT4x 0x44 |
| GPIO27 | I2C bus 0 SCL (20 kHz)                               |
| GPIO12 | I2C bus 1 SDA: ATtiny814 touch slider 0x0C           |
| GPIO14 | I2C bus 1 SCL (400 kHz)                              |
| GPIO32 | Touch data ready (active low)                        |
| GPIO5  | OLED SPI SCLK                                        |
| GPIO4  | OLED SPI MOSI                                        |
| GPIO15 | OLED CS                                              |
| GPIO2  | OLED DC                                              |
| GPIO22 | OLED reset                                           |
| GPIO25 | LED red                                              |
| GPIO21 | LED green                                            |
| GPIO19 | LED blue                                             |
| GPIO36 | Back power button (active low)                       |
| GPIO34 | USB present / charging (active low)                  |
| GPIO39 | Battery voltage (ADC, 1:2 divider)                   |
| GPIO35 | Charge current sense (ADC)                           |

## Basic Configuration

Hardware only: add `api:`, `ota:`, `captive_portal:` and your WiFi settings as usual.

```yaml file=config.yaml
```

## Full Configuration

```yaml url=https://github.com/nemethhh/esphome-qingping-cgdn1/blob/v0.2.0/cgdn1.yaml
```
