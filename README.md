# RC-VERSIONINFO

**RC-VERSIONINFO (Multilingual Version Info Template)** is a plain resource script template for Windows executables: it gives your `.exe` its file details in **English and Turkish**, and you can add any other language in a minute.

When you open an EXE's **Properties > Details** tab, Windows reads a `VERSIONINFO` resource inside the file. Most examples online only show one language, and many of them have a block name that does not match the `Translation` line, so the details come out blank. I made this template so the languages, the codes and the fallback are all correct from the start.

* **Plain RC syntax.** No `#define`, no `#include`. It works in Resource Hacker, `rc.exe` and `windres`.
* **Two languages ready.** English (`0409`) and Turkish (`041F`), both in Unicode.
* **English is the fallback.** Users with any other language see the English block.
* **Fill and compile.** Every value you need to change is a `<PLACEHOLDER>`.

<details>
  <summary><strong>Table of Contents</strong></summary>

- [Quick Start](#quick-start)
  - [Resource Hacker](#resource-hacker)
  - [rc.exe and windres](#rcexe-and-windres)
- [Placeholders](#placeholders)
- [How The Languages Work](#how-the-languages-work)
  - [Adding A New Language](#adding-a-new-language)
- [Language Codes](#language-codes)
- [Code Page Codes](#code-page-codes)
- [Fields](#fields)
- [Header Values](#header-values)
- [Common Mistakes](#common-mistakes)
- [Extras](#extras)

</details>

---

# Quick Start

1. Download [`VERSIONINFO.rc`][__VERSIONINFO_RC__].
2. Replace every `<PLACEHOLDER>` with your own value.
3. Compile it into your EXE with one of the tools below.

> **Fill every `<...>` before compiling.**
>
> **`FILEVERSION` and `PRODUCTVERSION` only take numbers, so a placeholder left there is a syntax error.**

## Resource Hacker

1. Open your `.exe` in Resource Hacker.
2. If it already has **Version Info**, open it, select all the text and paste the filled script over it.
3. If it has no Version Info, use **Action > Add using Script Template > VERSIONINFO** and paste the script.
4. Press **Compile Script** (`F5`), then **File > Save**.

The Resource Hacker editor already works with Unicode, so Turkish letters and `©` stay correct without anything extra.

## rc.exe and windres

These two read the file as plain bytes, so they need to know it is UTF-8. Save the file as **UTF-8** and add this line at the very top, **only for these tools**:

```rc
#pragma code_page(65001)
```

Microsoft Resource Compiler (Visual Studio / Windows SDK):

```bat
rc /nologo VERSIONINFO.rc
link main.obj VERSIONINFO.res
```

MinGW:

```sh
windres VERSIONINFO.rc -O coff -o VERSIONINFO.res
gcc main.c VERSIONINFO.res -o <FILE_NAME>.exe
```

---

# Placeholders

| Placeholder                       | What to write                | Example                                                      |
| --------------------------------- | ---------------------------- | ------------------------------------------------------------ |
| `<MAJOR>,<MINOR>,<PATCH>,<BUILD>` | Version as four numbers      | `1,4,0,2`                                                    |
| `<VERSION>`                       | The same version as text     | `1.4.0.2`                                                    |
| `<BUILD>`                         | Build number or year         | `2026`                                                       |
| `<BUILD_DATE>`                    | Build date                   | `2026-11-17`                                                 |
| `<COMPANY_NAME>`                  | Company or team name         | `Example Lab.`                                               |
| `<DEVELOPER_NAME>`                | Your name                    | `Jane Doe`                                                   |
| `<PRODUCT_NAME>`                  | Product name                 | `MY_APP`                                                     |
| `<FILE_NAME>`                     | EXE name without `.exe`      | `MY_APP`                                                     |
| `<DESCRIPTION_EN>`                | Short description in English | `Connect a PC to another via LAN`                            |
| `<DESCRIPTION_TR>`                | Short description in Turkish | `Bir bilgisayarı LAN üzerinden başka bir bilgisayara bağlar` |
| `<COMMENTS_EN>`                   | Comment in English           | `Connect a PC to another via LAN`                            |
| `<COMMENTS_TR>`                   | Comment in Turkish           | `Bir bilgisayarı LAN üzerinden başka bir bilgisayara bağlar` |
| `<YEAR>`                          | Copyright year               | `2026`                                                       |
| `<SUPPORTED_OS>`                  | Supported Windows versions   | `Windows 10, Windows 11`                                     |
| `<URL>`                           | Website                      | `https://example.com`                                        |
| `<SUPPORT_EMAIL>`                 | Support e-mail               | `support@example.com`                                        |

Keep `FileDescription` short. **Task Manager shows it as the process name.**

---

# How The Languages Work

Every language is one `BLOCK` inside `StringFileInfo`. The block name is **8 hex digits**: the language code, then the code page.

```rc
BLOCK "041F04B0"
```

| Part   | Value         | Meaning          |
| ------ | ------------- | ---------------- |
| `041F` | Language code | Turkish (Turkey) |
| `04B0` | Code page     | Unicode (1200)   |

Then the `Translation` line lists the same pairs. **Every block must be listed here, with the same codes.** The first pair is the fallback for every language you did not add.

```rc
VALUE "Translation", 0x0409, 0x04B0, 0x041F, 0x04B0
```

> **Resource Hacker writes this line without commas (`0x0409 0x04B0`).** `rc.exe` and `windres` need the commas, so I am using them in the template.

## Adding A New Language

For example, German:

1. Copy one of the blocks.
2. Change its name to `040704B0`.
3. Translate the text values.
4. Add the pair to the end of the `Translation` line:

```rc
VALUE "Translation", 0x0409, 0x04B0, 0x041F, 0x04B0, 0x0407, 0x04B0
```

---

# Language Codes

| Language                 | Code (hex) | Code (decimal) |
| ------------------------ | ---------- | -------------- |
| Neutral                  | `0000`     | 0              |
| English (United States)  | `0409`     | 1033           |
| English (United Kingdom) | `0809`     | 2057           |
| Turkish                  | `041F`     | 1055           |
| Azerbaijani (Latin)      | `042C`     | 1068           |
| German (Germany)         | `0407`     | 1031           |
| French (France)          | `040C`     | 1036           |
| Spanish (Spain, modern)  | `0C0A`     | 3082           |
| Italian (Italy)          | `0410`     | 1040           |
| Portuguese (Brazil)      | `0416`     | 1046           |
| Portuguese (Portugal)    | `0816`     | 2070           |
| Dutch (Netherlands)      | `0413`     | 1043           |
| Polish                   | `0415`     | 1045           |
| Russian                  | `0419`     | 1049           |
| Ukrainian                | `0422`     | 1058           |
| Greek                    | `0408`     | 1032           |
| Arabic (Saudi Arabia)    | `0401`     | 1025           |
| Hebrew                   | `040D`     | 1037           |
| Japanese                 | `0411`     | 1041           |
| Korean                   | `0412`     | 1042           |
| Chinese (Simplified)     | `0804`     | 2052           |
| Chinese (Traditional)    | `0404`     | 1028           |

---

# Code Page Codes

**Use `04B0` (Unicode) for every block.** It holds every letter of every language. The others are here so you can read old EXE files.

| Code page                  | Code (hex) | Code (decimal) |
| -------------------------- | ---------- | -------------- |
| **Unicode**                | **`04B0`** | **1200**       |
| 7-bit ASCII                | `0000`     | 0              |
| Central European           | `04E2`     | 1250           |
| Cyrillic                   | `04E3`     | 1251           |
| Western European           | `04E4`     | 1252           |
| Greek                      | `04E5`     | 1253           |
| Turkish                    | `04E6`     | 1254           |
| Hebrew                     | `04E7`     | 1255           |
| Arabic                     | `04E8`     | 1256           |
| Japanese (Shift-JIS)       | `03A4`     | 932            |
| Chinese Simplified (GBK)   | `03A8`     | 936            |
| Korean                     | `03B5`     | 949            |
| Chinese Traditional (Big5) | `03B6`     | 950            |

---

# Fields

Standard fields are defined by Windows. Custom fields are my own additions: they stay inside the EXE and you can read them with Resource Hacker or similar tools, but **the Explorer Details tab does not show them**.

| Field              | Type     |
| ------------------ | -------- |
| `CompanyName`      | Standard |
| `FileDescription`  | Standard |
| `FileVersion`      | Standard |
| `InternalName`     | Standard |
| `OriginalFilename` | Standard |
| `ProductName`      | Standard |
| `ProductVersion`   | Standard |
| `LegalCopyright`   | Standard |
| `LegalTrademarks`  | Standard |
| `Comments`         | Standard |
| `Developer`        | Custom   |
| `Signer`           | Custom   |
| `CopyrightOwner`   | Custom   |
| `CopyrightNotice`  | Custom   |
| `Build`            | Custom   |
| `BuildDate`        | Custom   |
| `Debugger`         | Custom   |
| `SupportedOS`      | Custom   |
| `URL`              | Custom   |
| `SupportEmail`     | Custom   |

> **`Signer` is only text.** A real signature needs a code-signing certificate and `signtool`.

---

# Header Values

`FILEOS`:

| Value         | Meaning                              |
| ------------- | ------------------------------------ |
| **`0x40004`** | **Windows NT, 32/64-bit (use this)** |
| `0x40000`     | Windows NT                           |
| `0x4`         | Old 32-bit Windows (Windows 3.x)     |

`FILETYPE`:

| Value | Meaning              |
| ----- | -------------------- |
| `0x1` | Application (`.exe`) |
| `0x2` | Library (`.dll`)     |
| `0x3` | Driver               |
| `0x4` | Font                 |
| `0x7` | Static library       |

---

# Common Mistakes

* **Block name and `Translation` do not match.** The Details tab shows blank fields.
* **Code page `04E4` with Turkish text.** Letters like `ı`, `ş`, `ğ` break. Use `04B0`.
* **`\xA9` instead of `©`.** With UTF-8 in `rc.exe` or `windres`, the whole string can come out empty. Write `©` directly.
* **Version text and numbers do not match.** `FILEVERSION 1,4,0,2` should go with `"1.4.0.2"`, not `"1.04.00.02"`.

---

# Extras

If something is wrong or you want a new language in the tables, open an issue: **[Issues][__ISSUES__]**

© 2026 Teoman Deniz

[__VERSIONINFO_RC__]: https://github.com/TeomanDeniz/RC-VERSIONINFO/blob/main/VERSIONINFO.rc
[__ISSUES__]: https://github.com/TeomanDeniz/RC-VERSIONINFO/issues
