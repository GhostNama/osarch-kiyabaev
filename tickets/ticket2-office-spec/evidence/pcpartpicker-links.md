# Direct product links and compatibility

**Proves:** every price in budget.csv comes from a product card of a vendor that sells in Kazakhstan, taken on 2026-11-11 or 2026-11-12 (the date of each price is in budget.csv), and the parts are compatible. Each link is a product page, not a catalogue or an aggregator; the saved page is in vendor-quotes/ under the file name shown.

| Part | Used in | Price, KZT | Direct link | Saved as (vendor-quotes/) |
|---|---|---|---|---|
| Ryzen 5 7600X OEM | accountant, sales | 95,462 | https://kaspi.kz/shop/p/amd-ryzen-5-7600x-oem-107747431/ | cpu-r5-7600x-kaspi-2026-11-11.pdf |
| Ryzen 7 7700 OEM | web developer, designer | 102,467 | https://kaspi.kz/shop/p/amd-ryzen-7-7700-oem-110124370/ | cpu-r7-7700-kaspi-2026-11-11.pdf |
| Gigabyte B650 Eagle | all four | 78,832 | https://kaspi.kz/shop/p/gigabyte-b650-eagle-131867973/ | board-b650-eagle-kaspi-2026-11-12.pdf |
| DDR5 2x16 GB AGI Turbosprint | web developer, designer | 309,990 | https://shop.kz/offer/ddr-5-dimm-32-gb-6000-mhz-agi-turbosprint-rgb-2x16gb-kit-black-box-agi6csj16ud858-dc/ | ram-32gb-agi-shopkz-2026-11-11.pdf |
| DDR5 16 GB Apacer (1 stick) | accountant, sales | 121,500 | https://shop.kz/offer/ddr-5-dimm-16-gb-5600-mhz-apacer-box-fl-16g2c-pkh/ | ram-16gb-apacer-shopkz-2026-11-12.pdf |
| Kingston NV3 1 TB | all four | 81,990 | https://kaspi.kz/shop/p/ssd-kingston-snv3s-1000g-1000-gb-123793481/ | ssd-nv3-kaspi-2026-11-11.pdf |
| MSI MAG A650BNL | all four | 25,590 | https://shop.kz/offer/blok-pitaniya-atx-650-w-msi-mag-a650bnl/ | psu-a650bnl-shopkz-2026-11-11.pdf |
| DeepCool AK400 Digital SE | all four | 18,400 | https://kaspi.kz/shop/p/deepcool-ak400-digital-se-r-ak400-bkadmn-gjd-147797761/ | cooler-ak400-kaspi-2026-11-11.pdf |
| Wintek Tornado Pro PS195 | all four | 19,905 | https://kaspi.kz/shop/p/wintek-tornado-pro-ps195-w-m-tg-belyi-141858358/ | case-wintek-kaspi-2026-11-11.pdf |
| MSI PRO MP253 24.5" | accountant, sales | 50,990 | https://kaspi.kz/shop/p/24-5-msi-pro-mp253-chernyi-139645233/ | monitor-mp253-kaspi-2026-11-11.pdf |
| MSI MAG275QF 27" | web developer, designer | 97,675 | https://kaspi.kz/shop/p/27-msi-mag275qf-chernyi-129123872/ | monitor-mag275qf-kaspi-2026-11-11.pdf |
| Windows 11 Home OEM | accountant, sales, designer | 30,400 | https://skysoft.kz/product/microsoft-windows-11-home-oem/ | os-win11-home-skysoft-2026-11-11.pdf |
| Windows 11 Pro 64-bit OEM DVD (FQC-10547) | web developer | 46,800 | https://kaspi.kz/shop/p/microsoft-windows-11-pro-64-bit-129147155/ | os-win11-pro-kaspi-2026-11-12.pdf |
| Palit RTX 5050 Dual | designer | 226,140 | https://greenprice.kz/videokarta-palit-geforce-rtx-5050-dual-ne65050019p1-gb2070d-8-gb-chernyj/ | gpu-rtx5050-greenprice-2026-11-11.pdf |

## Compatibility checks (from the product pages)

| Check | Result |
|---|---|
| CPU socket | Ryzen 5 7600X and Ryzen 7 7700 are AM5; B650 Eagle is AM5 |
| Memory | DDR5 on a DDR5 board with 4 slots; 2 stay free for expansion |
| CPU graphics | both CPUs have integrated graphics; the board has HDMI and DisplayPort outputs |
| Cooler vs case | AK400 Digital SE is 157 mm; the case allows 160 mm: fits with 3 mm to spare |
| Graphics card vs case | the Palit RTX 5050 Dual is 261 mm long; the case allows up to 340 mm: fits with 79 mm to spare |
| Power | 650 W unit with 2x (6+2)-pin graphics connectors; the designer's graphics card is the largest load |

## Pre-built quotes (Technodom, answer to question 2)

**Proves:** the price of the same four machines bought pre-built from one vendor that delivers in Kazakhstan, taken on 2026-11-12.

| Machine | Used for | Price, KZT | Direct link | Saved as (vendor-quotes/) |
|---|---|---|---|---|
| Neo Office 73 (Core i3-12100 / 16 GB / SSD 1 TB) | accountant, sales | 213,990 | https://www.technodom.kz/p/kompyuter-neo-office-73-ci3-12100-16gb-ssd-1tb-h610m-xg-cronus-291757 | prebuilt-neo-office-73-technodom-2026-11-12.pdf |
| NEO Game 88 (Core i5-13400F / RTX 5060 8 GB / 32 GB DDR5 / SSD 1 TB) | web developer, designer | 629,990 | https://www.technodom.kz/p/igrovoy-kompyuter-neo-game-88-ci5-13400f-rtx-5060-8gb-32-gb-ssd-1tb-h610m-infinity-mini-wh-295012 | prebuilt-neo-game-88-technodom-2026-11-12.pdf |
