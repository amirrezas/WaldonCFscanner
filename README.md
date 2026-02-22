# ⚡ WaldonCFscanner-python | Advanced Xray-Core VLESS Verifier

[![Python 3.8+](https://img.shields.io/badge/python-3.8+-blue.svg)](https://www.python.org/downloads/)
[![Platform](https://img.shields.io/badge/platform-Windows%20%7C%20Linux%20%7C%20macOS-lightgrey.svg)]()
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

*(⬇️ برای مطالعه نسخه فارسی به پایین صفحه مراجعه کنید)*

An incredibly fast, highly-optimized Cloudflare IP Scanner engineered specifically to bypass severe internet censorship infrastructures (such as the Great Firewall) using modern proxy protocols.

Created by [@amirrezas](https://github.com/amirrezas). Inspired by the works of MortezaBashsiz and the global anti-censorship community.

## 🌍 The Ethical Standpoint: Internet as a Human Right
In 2016, the United Nations Human Rights Council passed a resolution unequivocally condemning internet shutdowns, affirming that *"the same rights that people have offline must also be protected online."* Access to information, freedom of expression, and digital privacy are fundamental human rights. 

This tool exists because no government or entity should have the power to artificially isolate its citizens from the global community. **WaldonCFscanner** empowers individuals to bypass oppressive firewalls, resist Deep Packet Inspection (DPI), and reclaim their inalienable right to the open web.

---

## 🏗️ System Architecture: Why This Scanner Stands Out

Traditional Cloudflare scanners rely on simple ICMP pings or basic TCP handshakes. While those methods verify that an edge node is reachable, they completely fail to account for Deep Packet Inspection (DPI). A node might return a 20ms ping, but the firewall will instantly drop the connection the moment it detects unauthorized SNIs or proxy payloads.



**WaldonCFscanner** resolves this by verifying the *actual proxy tunnel protocol*. It utilizes an aggressive, asynchronous **4-Stage Hardware-Aware Pipeline**:

1. **🛡️ Stage 1: Asynchronous TCP Probing:** Utilizes non-blocking sockets to rapidly prune dead subnets and verify Layer 4 reachability (processing thousands of IPs per second).
2. **⚡ Stage 2: TLS SNI Injection:** Establishes a cryptographic TLS handshake using a permitted Server Name Indication (SNI). This confirms the IP belongs to the Cloudflare network and verifies that the firewall is not actively blackholing the domain via SNI filtering.
3. **🚀 Stage 3: HTTP Throughput Benchmarking:** Measures actual payload delivery capacity (Goodput) by attempting to stream a 1MB payload directly from the edge node, ensuring the IP is not aggressively throttled.
4. **🔐 Stage 4: Headless Xray-Core Verification (The VIP Room):** Only the fastest, verified IPs reach this stage. The engine dynamically spawns a localized, headless instance of the official `Xray-core` daemon. It injects your specific VLESS configuration, binds to a random local port, and initiates a live WebSocket/TLS proxy tunnel to measure the true cryptographically-verified Time-to-First-Byte (TTFB) latency.

---

## ✨ Zero-Setup Execution 
We believe anti-censorship tools should be accessible to everyone. You do not need to understand Python virtual environments, `pip`, or Go binaries to use this tool. 

If you have Python installed, simply execute the script. The bootstrap engine will automatically:
1. Detect and install missing Python dependencies (`aiohttp`, `textual`).
2. Query the official XTLS GitHub API to fetch the latest `Xray-core` release tailored to your OS architecture.
3. Extract the binary, apply necessary execution permissions (on Unix systems), and clean up residual files.

---

## 🛠️ Step-by-Step Installation & Setup

### 🪟 Windows
1. **Install Python:** Download Python from [python.org](https://www.python.org/downloads/). **Crucial:** During installation, you *must* check the box that says **"Add Python to PATH"**.
2. **Download the Project:** Click the green **"Code"** button at the top of this GitHub repository and select **"Download ZIP"**. Extract the folder to your computer.
3. **Run the Scanner:** Open Command Prompt (`cmd`), navigate to your extracted folder, and run:
   ```cmd
   python scanner.py
   ```

### 🐧 Linux (Ubuntu / Debian)
1. **Install Python:** Open your terminal and install Python and pip:
   ```bash
   sudo apt update && sudo apt install python3 python3-pip
   ```
2. **Clone the Project:**
   ```bash
   git clone [https://github.com/amirrezas/WaldonCFscanner-python.git](https://github.com/amirrezas/WaldonCFscanner-python.git)
   cd WaldonCFscanner-python
   ```
3. **Run the Scanner:**
   ```bash
   python3 scanner.py
   ```

### 🍏 macOS
1. **Install Python:** Open your terminal and run `brew install python` (requires Homebrew).
2. **Clone and Run:**
   ```bash
   git clone [https://github.com/amirrezas/WaldonCFscanner-python.git](https://github.com/amirrezas/WaldonCFscanner-python.git)
   cd WaldonCFscanner-python
   python3 scanner.py
   ```

---

## ⚙️ Bi-Directional Configuration Generation
You can personalize the scanner to automatically generate ready-to-use VPN profiles based on your private server.

* **Via JSON:** Place your server's base VLESS config inside a file named `config.json` in the root folder before running.
* **Via URI (Clipboard):** While the program is running, paste your `vless://...` clipboard link directly into the **VLESS URI** input box inside the Terminal Dashboard.

When the scanner discovers a top-tier IP, it will create an `output_configs/` directory containing customized `.json` client files and a `vless_links.txt` file packed with shareable, high-speed URIs.

## 🤝 Debugging & Support
If the scanner experiences anomalous behavior or routing failures, click the **"Save Log"** button in the TUI dashboard. This dumps the asynchronous stack traces and Xray daemon errors into a `scanner_error.log` file. Please open an [Issue on GitHub](https://github.com/amirrezas/WaldonCFscanner-python/issues) and attach this log to assist in continuous optimization.

---
---

# 🇮🇷 نسخه فارسی (Persian Version)

یک اسکنر آی‌پی کلودفلر (Cloudflare) فوق‌العاده سریع و بهینه‌سازی شده، که منحصراً برای دور زدن فیلترینگ شدید اینترنت و سیستم‌های بازرسی عمیق بسته‌ها (GFW) با استفاده از پروتکل‌های مدرن پروکسی طراحی شده است.

## 🌍 دیدگاه اخلاقی: دسترسی به اینترنت به عنوان حقوق بشر
در سال ۲۰۱۶، شورای حقوق بشر سازمان ملل متحد قطعنامه‌ای را تصویب کرد که در آن قطع اینترنت را صراحتاً محکوم کرده و تاکید می‌کند که *"حقوقی که افراد در دنیای آفلاین دارند، باید در فضای آنلاین نیز محافظت شود"*. دسترسی به اطلاعات، آزادی بیان و حریم خصوصی دیجیتال، از حقوق بنیادین بشر هستند.

این ابزار ساخته شده است زیرا هیچ دولت یا نهادی نباید این قدرت را داشته باشد که شهروندان خود را از جامعه جهانی جدا کند. **WaldonCFscanner** به افراد این قدرت را می‌دهد تا فایروال‌های سرکوبگر را دور بزنند، در برابر سیستم‌های فیلترینگ مقاومت کنند و حق مسلم خود را برای دسترسی به اینترنت آزاد پس بگیرند.

---

## 🏗️ معماری سیستم: تفاوت این اسکنر با سایر ابزارها

اسکنرهای سنتی کلودفلر صرفاً بر پایه پینگ (ICMP) یا اتصال اولیه TCP کار می‌کنند. این روش‌ها در برابر فیلترینگ‌های پیشرفته (DPI) کاملاً ناکارآمد هستند؛ زیرا ممکن است یک آی‌پی پینگ عالی بدهد، اما فایروال به محض تشخیص ترافیک پروکسی، اتصال را قطع کند.

**ابزار WaldonCFscanner** این مشکل را با استفاده از یک **موتور ۴-مرحله‌ای پیشرفته** حل کرده است:
۱. **بررسی لایه ۴ (TCP):** اسکن ناهمگام (Asynchronous) هزاران آی‌پی در ثانیه برای یافتن سرورهای روشن.
۲. **تزریق TLS SNI:** انجام هندشیک (Handshake) رمزنگاری شده برای اطمینان از مسدود نبودن آی‌پی توسط فایروال.
۳. **تست توان عملیاتی (Throughput):** دانلود یک فایل ۱ مگابایتی برای سنجش ظرفیت واقعی پهنای باند آی‌پی.
۴. **تأیید نهایی توسط هسته Xray:** آی‌پی‌های برنده وارد این مرحله می‌شوند. برنامه به صورت خودکار یک هسته `Xray-core` را در پس‌زمینه اجرا کرده و تونل VLESS شما را به صورت واقعی تست می‌کند تا پینگ دقیق و رمزنگاری‌شده (TTFB) را محاسبه کند.

---

## ✨ اجرای هوشمند و بدون نیاز به تنظیمات
ما معتقدیم ابزارهای ضد سانسور باید برای همه، حتی افراد بدون دانش فنی، قابل استفاده باشند. شما نیازی به درک مفاهیم پایتون یا گیت‌هاب ندارید. 

فقط کافیست اسکنر را اجرا کنید. برنامه به صورت خودکار:
۱. کتابخانه‌های مورد نیاز پایتون را شناسایی و نصب می‌کند.
۲. از طریق API رسمی گیت‌هاب، جدیدترین نسخه `Xray-core` مناسب با سیستم‌عامل شما را دانلود، استخراج و آماده‌ی اجرا می‌کند.

---

## 🛠️ راهنمای نصب سریع

### 🪟 ویندوز
۱. **نصب پایتون:** پایتون را از [python.org](https://www.python.org/downloads/) دانلود و نصب کنید. **(بسیار مهم: در صفحه اول نصب، حتماً تیک گزینه "Add Python to PATH" را بزنید).**
۲. **دانلود برنامه:** در همین صفحه گیت‌هاب، روی دکمه سبز رنگ **"Code"** کلیک کرده و **"Download ZIP"** را انتخاب کنید. پوشه را از حالت فشرده خارج کنید.
۳. **اجرای اسکنر:** خط فرمان (`cmd`) را باز کنید، به مسیر پوشه بروید و دستور زیر را وارد کنید:
   ```cmd
   python scanner.py
   ```

### 🐧 لینوکس و 🍏 مک‌اواس
۱. **نصب پایتون:** (در لینوکس دستور `sudo apt install python3 python3-pip` را اجرا کنید).
۲. **دریافت و اجرای برنامه:**
   ```bash
   git clone [https://github.com/amirrezas/WaldonCFscanner-python.git](https://github.com/amirrezas/WaldonCFscanner-python.git)
   cd WaldonCFscanner-python
   python3 scanner.py
   ```

---

## ⚙️ تولید خودکار کانفیگ و لینک VLESS
شما می‌توانید اسکنر را شخصی‌سازی کنید تا پس از یافتن آی‌پی‌های سالم، کانفیگ‌های اختصاصی شما را بسازد:
* **روش اول:** فایل `config.json` سرور خود را قبل از اجرا، در پوشه اصلی برنامه قرار دهید.
* **روش دوم:** پس از اجرای برنامه، لینک کپی شده خود را (مثلاً `vless://...`) مستقیماً در کادر **VLESS URI** در محیط برنامه پیست (Paste) کنید!

پس از پیدا شدن آی‌پی‌های موفق، برنامه پوشه‌ای به نام `output_configs/` می‌سازد و فایل‌های آماده‌ی اجرای Json و لینک‌های متنی (`vless_links.txt`) را در آن ذخیره می‌کند.

## 🤝 عیب‌یابی و گزارش خطا
اگر اسکنر در حین کار متوقف شد، روی دکمه **"Save Log"** در محیط برنامه کلیک کنید. سپس فایل `scanner_error.log` تولید شده را در بخش [Issues گیت‌هاب](https://github.com/amirrezas/WaldonCFscanner-python/issues) آپلود کنید تا ما بتوانیم الگوریتم برنامه را بهبود ببخشیم.