# Ticket 2 - Office hardware specification

## 1. What was asked

> From: Daniyar K. (Director)
> Subject: computers for the new office
>
> We are opening the second office and need four computers — accountant, sales, web developer, designer. Budget 3,000,000 tenge for everything. Everyone says we should just buy the same four machines, is that right? Make sure they last a few years. Monitors included.

## 2. What was actually needed

**The director's assumption is wrong.** Four identical machines overspend on the roles that need little and starve the roles that need a lot: the accountant does not need the memory a web developer's Docker containers need, and only the designer gains anything from a graphics card. The four roles carry different loads, so the money has to move to where the bottleneck is. Two of my four builds are the same machine (accountant and sales) because their loads fit the same hardware; the point is that the web developer and the designer cannot share it.

What the request left out, and what I assumed instead:
- **"Budget 3,000,000 tenge for everything".** It does not say whether keyboards, mice and a UPS are inside that sum. I assumed the budget covers the four computers, the four monitors and the operating system licences; keyboards, mice and a UPS are bought separately and are not in budget.csv.
- **"Computers".** Desktop or laptop? I assumed desktops: "monitors included" and "last a few years" point to machines that can be upgraded part by part, while a laptop's memory and graphics usually cannot be changed.

What I would have asked the director: whether keyboards, mice and a UPS are in the 3,000,000, and whether he wants desktops or laptops.

## 3. What I did

1. Described each role by its load, bottleneck and trade-off (builds.md).
2. Chose one shared platform (AM5, same board, SSD, PSU, cooler and case) and varied only CPU, memory, graphics and monitor by role. Alternative considered: four different platforms, rejected because spare parts, drivers and assembly instructions would multiply.
3. Priced every part from a vendor product card in Kazakhstan on 2026-11-11 and 2026-11-12 (budget.csv, evidence/pcpartpicker-links.md).
4. Replaced parts that were not in stock or did not fit the budget: RTX 4060 -> RTX 5050 (the cheapest in-stock RTX 5060 was 311,040 KZT), 4K monitor -> 27" 1440p, 64 GB -> 32 GB for the web developer.

| Role | Total, KZT | budget.csv rows |
|---|---|---|
| Accountant | 523,069 | 2-10 |
| Sales manager | 523,069 | 11-19 |
| Web developer | 781,649 | 20-28 |
| Designer | 991,389 | 29-38 |
| **Total** | **2,819,176** | |
| Reserve for price changes | 180,824 | |

### Answers to the three questions

**1. Which machine will I upgrade first, eighteen months from now, and why?**
The designer's graphics card. It is the part I cut to stay in budget (an RTX 5050 instead of an RTX 5060, which cost 311,040 KZT), it can be replaced without touching anything else (the 650 W power supply has two 6+2-pin connectors and the case takes cards up to 340 mm), and long exports and Adobe's graphics features are where this machine will meet its limit first with 8 GB of video memory. The designer's memory is already 32 GB with two free slots.

**2. The same four as pre-built machines: what does the company gain and lose?**
Vendor: Technodom (delivers across Kazakhstan). The TechnoGaming 285 (550,990 KZT) was out of stock and comes in a glass case, so I priced a machine that is in stock. Both quotes are dated 2026-11-12 and saved in evidence/vendor-quotes/.

| Role | Pre-built machine | Machine, KZT | + Windows 11 + monitor, KZT | Total, KZT |
|---|---|---|---|---|
| Accountant | Neo Office 73 (Core i3-12100, 16 GB, SSD 1 TB) | 213,990 | 30,400 + 50,990 | 295,380 |
| Sales manager | Neo Office 73 (same) | 213,990 | 30,400 + 50,990 | 295,380 |
| Web developer | NEO Game 88 (Core i5-13400F, RTX 5060 8 GB, 32 GB DDR5, SSD 1 TB) | 629,990 | 46,800 + 97,675 | 774,465 |
| Designer | NEO Game 88 (same) | 629,990 | 30,400 + 97,675 | 758,065 |
| **Total** | | | | **2,123,290** |

The same four built from parts cost 2,819,176 KZT, so the pre-built set is about 696,000 KZT (25%) cheaper at today's prices. Neither pre-built machine comes with an operating system (the Neo Office 73 and the NEO Game 88 are both sold without one), so Windows 11 is added at the same prices as in my builds; the monitors are the same models as in my builds.

What the company gains by not building: a lower price today, one vendor for warranty and support, machines that arrive assembled and tested, and no assembly time.
What it loses: the machine does not follow the role. The web developer and the designer get the same gaming machine, and the RTX 5060 is wasted on the web developer's work, while the accountant and the sales manager get a four-core Core i3, weaker than my six-core CPUs for video calls. The platform is the older LGA1700 socket, so there is less of an upgrade path than on my AM5 builds, and the case, the cooling and the memory slots are whatever the vendor chose.

**3. Who will complain about performance within a month, and about what?**
The web developer, about slow builds and slow containers. Two IDEs, 10-15 Docker containers and up to 50 browser tabs can fill 32 GB together; once the system starts swapping, Node.js/Vite builds and the database containers slow down visibly, and a faster CPU would not fix it. I chose 32 GB over 64 GB because a 64 GB kit cost more than half of this machine's budget, so this is the compromise he will feel first. The board has four memory slots, so more memory can be added later.

## 4. Evidence

- `budget.csv`: every part with price, source and date.
- `evidence/pcpartpicker-links.md`: direct links to the product cards and the compatibility checks.
- `evidence/vendor-quotes/`: the saved product pages of the parts and of the two pre-built machines, named in the tables of pcpartpicker-links.md; they prove the prices and the dates.

## 5. What I would do differently

I first priced the memory from a catalogue list and wrote 139,990 KZT for the 2x16 GB kit; the product card said 309,990 KZT. Next time I take every price from the product card and the stock status before building a configuration around it: three of my first choices (RTX 4060, a 4K monitor, a Dell monitor) were listed but not in stock.
