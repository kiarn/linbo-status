# 🖥️ linbo-status

A single-page dashboard showing the **last LINBO synchronisation** of every client of a [linuxmuster.net](https://www.linuxmuster.net) server, grouped by hardware group.

It reads the report written by `lmncli linbo lastsync`: no backend, no database, no build step. Just one HTML file next to a CSV file.

---

## ✨ Features

| | Feature | Description |
|---|---|---|
| 🚦 | **Status at a glance** | Every image sync is colour-coded: 🟢 less than 7 days, 🟠 7 to 30 days, 🔴 more than 30 days or never. |
| 🗂️ | **Tabs per hardware group** | One tab per LINBO group plus *All groups*. Each tab shows its worst status and a `visible/total` counter (keyboard: ← →). |
| 📊 | **Summary tiles** | Number of clients per status for the selected tab. |
| 🔎 | **Search** | Filter by hostname or IP. |
| ⚠️ | **Problems only** | Hide the clients whose images are all up to date. |
| 🌙 | **Not today** | Keep only the clients with at least one image not synchronised since midnight, to check the nightly syncs. |
| 🚀 | **Last start** | Optional column with the last boot date and started OS (`--last-start`). It is coloured with the same thresholds but does not change the client status. |
| ⏰ | **Stale report warning** | A banner appears when the report has not been rewritten for more than 26 hours, i.e. when the cron job is broken. |
| 📂 | **Open a file** | Open a report from your machine with the button or by drag and drop, without any web server. |
| 🌗 | **Light and dark mode** | Follows the system setting. |
| 🔒 | **Fully self-contained** | No CDN, no external font, no tracking: the page never contacts any other server. |

All filters (search, *Problems only*, *Not today*, tabs) can be combined.

---

## 📋 Requirements

- A linuxmuster.net 7.4 server with `linuxmuster-cli7`
  - `>= 7.4.12` for the *Last start* column (`--last-start`)
- A web server able to serve static files (Apache, nginx, ...)
- Any recent browser

---

## 📦 Installation

**1. Copy the page** to a directory served by your web server:

```bash
mkdir -p /var/www/html/linbo
cp index.html /var/www/html/linbo/
```

**2. Write the report periodically** with a cron job, e.g. in `/etc/cron.d/linbo-status`:

```cron
*/30 * * * * root /usr/sbin/lmncli --csv linbo lastsync --last-start > /var/www/html/linbo/lastsync.csv
```

Drop `--last-start` if you do not need the *Last start* column.

**3. Open** `https://<server>/linbo/` in your browser. 🎉

> [!TIP]
> The report contains hostnames and IP addresses of the clients: restrict access to this directory (authentication or IP filtering) in your web server configuration.

---

## ⚙️ Configuration

| Parameter | Default | Description |
|---|---|---|
| `?f=<file>` | `lastsync.csv` | Name of the report to load, relative to the page, e.g. `index.html?f=school2.csv`. |

The report date is taken from the HTTP `Last-Modified` header of the CSV file, so the web server must send it (Apache and nginx do by default for static files).

---

## 📄 Report format

The page reads the output of `lmncli --csv linbo lastsync` (`;` separated) and of `lmncli --raw linbo lastsync` (tab separated); the separator is detected automatically.

| Column | Required | Description |
|---|---|---|
| `Hostname` | ✅ | Client name |
| `IP` | | Client IP address |
| `Last synchronisation for <image>` | ✅ | One set of columns per image |
| `Status for <image>` | | `success`, `warning` or `danger`; computed with the 7 / 30 days thresholds if missing |
| `Applied version of <image>` | | Date of the image applied on the client |
| `Group` | | Hardware group, used for the tabs |
| `Last start`, `Last started OS` | | Only written with `--last-start` |

Older reports, without the `Group` column or with raw Python dicts in the cells, are still supported.

---

## 🧪 Demo

Without any report next to the page, the dashboard displays demo data and explains how to set up the cron job. Open `index.html` directly in your browser to try it.

---

## 📜 License

[GNU GPL v3](LICENSE)
