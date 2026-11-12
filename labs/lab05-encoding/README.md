# Lab 5 - Encoding forensics

## Diagnosis (before the fix)

The original `evidence/staff-broken.csv` was checked before any change.
SHA-256 (Windows `Get-FileHash` and Ubuntu `sha256sum` agree), also at the end of the lab:
`919027f1f8c71d85cba3f12aad0f6c927d4e0450cfb2a2d63378a69f14cab270`

`file` says: `Non-ISO extended-ASCII text, with CRLF line terminators` (evidence/file-before.txt)

First 16 bytes (evidence/hexdump-before.txt): `d2 e0 e1 2e b9 3b d4 c8 ce 3b c4 ee eb e6 ed ee`

| Offset | Bytes | What they are |
|---|---|---|
| 00 | d2 | `Т`, first character, one byte: no BOM (a BOM would start `ef bb bf`) |
| 01-02 | e0 e1 | `а` `б`: Cyrillic letters, one byte each (header "Таб") |
| 03 | 2e | `.` ASCII |
| 04 | b9 | `№`, one byte in RK1048 (U+2116) |
| 05 | 3b | `;` separator (semicolon) |
| 06-08 | d4 c8 ce | `Ф` `И` `О` |
| 09 | 3b | `;` separator |
| 0a-0f | c4 ee eb e6 ed ee | `Д` `о` `л` `ж` `н` `о` ("Должно..." of "Должность") |

The line ending is not inside the first 16 bytes; it is shown by `cat -A` (`^M$` at each line end).

**Encoding: RK1048 (Kazakh single-byte code page)** - evidence:
- Not UTF-8: `iconv -f UTF-8 -t UTF-8` stops with "illegal input sequence at position 0"; there are no UTF-8 pairs like `d0 xx` and no BOM.
- Cyrillic letters are single bytes in the range 80-FF, so it is a single-byte code page.
- CP866 and KOI8-R give unreadable text.
- CP1251, RK1048 and PT154 all read the Russian letters, so the Kazakh letters decide. Printing every non-Russian character as a Unicode code point: CP1251 produced Є, Ї, Ј, Ћ, Ќ, Ґ, ў (U+0404, 0407, 0408, 040B, 040C, 0490, 045E): not Kazakh letters. PT154 produced Ҙ, Ҝ, ҫ (U+0498, 049C, 04AB): not Kazakh letters either. RK1048 produced only Ғ, Қ, қ, Ү, ү, ұ, Һ, Ә, ә, Ө, ө, І, і (U+0492, 049A, 049B, 04AE, 04AF, 04B1, 04BA, 04D8, 04D9, 04E8, 04E9, 0406, 0456).
- `iconv -f RK1048 -t UTF-8` decodes the whole file without error and every name reads correctly.

**Line ending: CRLF** - evidence: `cat -A` shows `^M$` at each line end; 26 CR bytes (`tr -cd '\r' | wc -c`) and 26 LF bytes (`tr -cd '\n' | wc -c`): CR count equals LF count, so every line ends in CRLF.

**BOM: absent** - evidence: the file starts with `d2 e0`, not `ef bb bf`.

## What each application was doing wrong

- **Excel:** double-clicking a CSV without a BOM, it assumes the Windows ANSI code page (CP1251 on a Russian-language Windows) instead of RK1048. Russian letters come out right because CP1251 and RK1048 agree on them; the Kazakh letters are wrong (the CP1251 reading gives Є and ў where Қ and ұ should be, as the `iconv -f CP1251` test above shows).
- **LibreOffice Calc:** it always shows the Text Import dialog and proposes a character set. A single-byte file without a BOM carries no label of its encoding, so Calc has to guess, and it cannot reliably tell RK1048 from the other single-byte code pages; the Kazakh letters can come out wrong. For the fixed file it proposed Unicode (UTF-8), chosen automatically (the dialog showed "Automatic - Unicode (UTF-8)" and detected `;` as the separator), and all names, including the Kazakh letters, read correctly in the preview.
- **cat in a UTF-8 terminal:** the terminal expects UTF-8; bytes 80-FF in this file are not valid UTF-8, so they print as replacement characters or noise. The bytes are intact, the decoder was wrong.

## Fix

Command / script: `scripts/fix.sh` (decodes with `iconv`, removes CR with `sed`, prepends a BOM).

Changed: encoding RK1048 -> UTF-8; line ending CRLF -> LF; BOM added, because Excel decided it: without a BOM, Excel double-click opens UTF-8 as ANSI and shows noise, and with a BOM Calc and `cat` are unaffected. The cost is that a script reading the first header sees `\ufeffТаб.№`.

| Reader | With BOM | Without BOM |
|---|---|---|
| Excel, double-click | correct | not tested; Excel would read UTF-8 as ANSI (see above) |
| LibreOffice Calc | correct | not tested; Calc proposes a charset in the import dialog |
| cat in UTF-8 terminal | correct (BOM invisible) | correct |
| script reading first header | header starts with an invisible BOM | clean |

## Verification

file after (evidence/file-after.txt): `Unicode text, UTF-8 (with BOM) text`
First 16 bytes after: `ef bb bf d0 a2 d0 b0 d0 b1 2e e2 84 96 3b d0 a4` - exactly one BOM, then "Таб.№;Ф".
`sha256sum staff-broken.csv` after the fix: unchanged, `919027f1...14cab270`.
CR bytes in `staff-fixed.csv`: 0 (`tr -cd '\r' < staff-fixed.csv | wc -c`).

Opened in: Microsoft Excel 2021 MSO (version 2608, build 16.0.20326.20072, 64-bit), LibreOffice Calc 26.8, `cat` on Ubuntu 26.04.1 LTS
- evidence/opens-correctly.png

Colleague's "open it in Notepad and Save As UTF-8": it works only if Notepad first decodes the file with the right code page, which is exactly the diagnosis this lab asks for, and Notepad cannot reliably tell RK1048 from CP1251, so it can silently corrupt the Kazakh letters.
