# Solar Powered Heated Blanket (SPHB)

An off-grid, solar-charged heated blanket — no mains power, no disposable
batteries, just a solar panel, a rechargeable battery, and an embedded
controller that manages heat output and charge safety. This repo holds
the firmware for the electronics.



## Why this matters

Staying warm without access to mains electricity is a real, ongoing
safety problem, not just a novelty:

- **4,950 excess winter deaths** in the UK were attributed to cold homes
  in winter 2022/23 alone, and excess winter deaths have run as high as
  **20,000–50,000 a year** over 2000–2019 ([ONS](https://www.ons.gov.uk/aboutus/transparencyandgovernance/freedomofinformationfoi/excesswinterdeaths2024to2025), [End Fuel Poverty Coalition](https://www.endfuelpoverty.org.uk/about-fuel-poverty/excess-winter-deaths-and-fuel-poverty/)).
- **4.5 million UK homes** were in fuel poverty as of late 2025 ([NEA](https://www.nea.org.uk/news-insights/fuel-poverty-charity-reveals-45-people-per-day-die-from-cold-homes/)).
- **1,474 people died while homeless** in the UK in 2023, with cold,
  hypothermia and exposure among the leading risks for people sleeping
  rough ([Single Homeless Project](https://www.shp.org.uk/homelessness-explained/the-impact-of-climate-change-on-homelessness/)).

This isn't a hypothetical use case: a solar-charged heated blanket built
by a Glasgow schoolgirl, Rebecca Young, was picked up and engineered into
a real product by Thales UK, who donated **150 units** to homelessness
charities in Glasgow — proof that this exact idea, done well, has already
kept vulnerable people warm ([BBC News](https://www.bbc.co.uk/news/articles/c9wyv1r0yjeo), [Primary Engineer](https://www.primaryengineer.com/thales-glasgow-have-turned-p7-pupil-rebeccas-solar-powered-heated-blanket-into-a-reality/)).

SPHB is my own independent build of that concept: a self-contained,
solar-rechargeable heating system with no reliance on the grid — useful
for rough sleepers, off-grid living, camping/outdoor use, or as emergency
warmth during power cuts and cold snaps.

## What it does

- **Charges from a solar panel** into an onboard battery, and **switches
  automatically into charge mode** once the panel/battery voltage crosses
  a safe threshold — cutting blanket power so the battery charges
  uninterrupted, and showing live charging wattage on screen.
- **Three selectable heat levels** (`HIGH` / `MEDIUM` / `LOW`) plus `OFF`,
  cycled with a single button — `MEDIUM`/`LOW` duty-cycle the heating
  element rather than running it flat out, stretching battery life so the
  blanket lasts longer per charge.
- **Live temperature display** so the user always knows how warm the
  blanket currently is.
- **Runs entirely on stored solar energy** — no mains outlet or
  disposable batteries required, making it usable anywhere.

## How it works

An Arduino-based controller ties together four jobs: sensing the solar
panel/battery voltage, sensing current draw, reading blanket temperature,
and driving the heating element through a relay. Every 250 ms it re-checks
temperature and charge state; the moment charging is detected, blanket
power is cut and the display switches to show charging wattage instead,
so the two functions — warming the user and refilling the battery — never
fight each other. Heat level is throttled at the software level by
time-slicing the relay rather than needing extra hardware for variable
power control.

## Hardware / pinout

| Signal | Pin | Notes |
|---|---|---|
| Relay (blanket heater) | `A1` | Drives the heating element |
| Relay power | `D9` | Powers the relay module |
| Button input | `D7` | Debounced, active HIGH |
| Button power | `D6` | Toggled HIGH to power the button circuit |
| Current sensor (ACS712) | `A0` | 5.0 V ref, 1023 resolution, 66 mV/A |
| Battery voltage sense | `A2` | Resistor divider: 30 kΩ / 7.5 kΩ |
| SHT35 temp/humidity | `SCL → A5`, `SDA → A4` | I2C, via Seeed_SHT35 library |
| LCD (16x2, parallel) | `RS=12, EN=11, D4=5, D5=4, D6=3, D7=2` | via `LiquidCrystal` library |

## Dependencies

- [`LiquidCrystal`](https://docs.arduino.cc/libraries/liquidcrystal/) (Arduino built-in)
- [`Seeed_SHT35`](https://github.com/Seeed-Studio/Seeed_SHT35) — temperature/humidity sensor
- [`ACS712`](https://github.com/RobTillaart/ACS712) — current sensor

## Repo contents

- [`sphb.ino`](sphb.ino) — full firmware for the blanket controller.

## Status / next steps

Firmware is functional on the bench. Open items: photos and a build log
of the physical blanket/panel/enclosure, real-world testing outdoors, and
possibly exploring a path to donate or replicate units the way the
Thales/Rebecca Young project did.
