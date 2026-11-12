# Four builds

## Common chassis (the same in all four)

Motherboard Gigabyte B650 Eagle (AM5, 4 DDR5 slots), Kingston NV3 1 TB SSD, MSI MAG A650BNL 650 W PSU, DeepCool AK400 Digital SE cooler (157 mm), Wintek Tornado Pro PS195 case (4 fans), Windows 11.
Why: one platform means one set of spare parts, one driver set and one assembly instruction, and every machine keeps a free upgrade path (4 memory slots, AM5 socket). What differs is only what the load actually uses: CPU, memory, graphics, monitor.
Compatibility checked on the product pages: cooler 157 mm fits the case limit of 160 mm; the case takes a graphics card up to 340 mm; AM5 board and AM5 CPUs; DDR5 memory; the board has HDMI and DisplayPort outputs for the CPUs' integrated graphics.

## Build 1 - accountant

**Load:** Excel with large workbooks, accounting software, PDFs and e-mail, one browser; eight hours a day, many documents open at once. Needs stability and responsiveness, not raw speed.
**Bottleneck:** memory. 16 GB is enough for the day but is the first thing that fills when several big workbooks are open; it is also a single stick, so memory runs in one channel and the integrated graphics are slower.
**Trade-off:** one 16 GB stick instead of a 2x8 kit (the kit was sold out; a second stick can be added later for dual channel and 32 GB), and no discrete graphics card because the Ryzen 5 7600X has integrated graphics. The money saved went into the shared chassis, not into a faster CPU.
**Total:** 523,069 KZT (budget.csv, rows 2-10)

## Build 2 - sales manager

**Load:** CRM in a browser, 30-40 tabs, Excel, presentations, several hours of video calls a day, often all at once.
**Bottleneck:** memory, not CPU. A browser with forty tabs plus a video call takes a large part of 16 GB, and screen sharing in a call uses the integrated graphics, which are slowed by single-channel memory.
**Trade-off:** the same hardware as the accountant's, deliberately: the loads differ, but both fit a six-core CPU with integrated graphics and 16 GB, and identical machines are interchangeable spares. I did not buy a second memory stick (121,500 KZT each) because the budget was used by the web developer and the designer.
**Total:** 523,069 KZT (budget.csv, rows 11-19)

## Build 3 - web developer

**Load:** front-end and back-end work, several IDEs open, 10-15 Docker containers (databases, Redis, API), Node.js/Vite builds and up to 50 browser tabs.
**Bottleneck:** memory first, then CPU threads. At 32 GB the containers and IDEs fit, but heavy environments start to swap, which slows builds.
**Trade-off:** no discrete graphics card (web development does not use it; the Ryzen 7 7700 integrated graphics drive a 1440p monitor). 32 GB instead of 64 GB: a 64 GB kit cost 573,000-643,000 KZT, more than half of this machine's budget; four slots let me add memory later. 27" 1440p monitor instead of 4K: the 4K models I priced were out of stock or far more expensive. Windows 11 Pro (OEM, 46,800 KZT) instead of Home (30,400 KZT) for Hyper-V and BitLocker; Docker also runs on Home through WSL2, but the 16,400 KZT difference is small for a machine that is used for containers all day.
**Total:** 781,649 KZT (budget.csv, rows 20-28)

## Build 4 - designer

**Load:** Photoshop, Illustrator and Figma, large layered files, long exports, a browser with reference material.
**Bottleneck:** memory (32 GB in two channels) and the screen. Long exports hold the CPU and graphics card busy for minutes, so the cooling matters: a tower cooler plus four case fans.
**Trade-off:** RTX 5050 instead of RTX 5060: the RTX 4060 I planned was out of stock at every vendor I checked, and the cheapest in-stock RTX 5060 was 311,040 KZT, which broke the budget. The 5050 has the hardware acceleration that Adobe uses; for 2D work the difference is small. The monitor is the MSI MAG275QF (101% sRGB) rather than a factory-calibrated one: it is a gaming monitor and has no factory calibration. A 1 TB NV3 is a budget SSD whose speed drops during long writes. Rejected alternative: a case with solid glass on the front, which looks better but chokes the airflow; the Wintek case has a mesh front panel and three front fans, so air reaches the CPU cooler and the graphics card.
**Total:** 991,389 KZT (budget.csv, rows 29-38)
