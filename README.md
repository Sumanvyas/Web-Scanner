# Automated Web Security Scanner

**Secure-Header, Cookie and CORS Misconfiguration Auditor**

> IBM SkillsBuild Cyber Security Bootcamp 2026 | BharatCares
> Project topic: **Automated Web Scanner** - a Python script that audits sites for secure headers and CORS flaws.
> Author: **Suman Vyas**

---

## 1. Project description

Many web breaches start with simple *security misconfigurations* (OWASP Top 10 - A05:2021): a missing `Content-Security-Policy`,
a session cookie without `HttpOnly`, or a CORS policy that trusts every origin. This project is a Python tool (delivered as a
Jupyter Notebook) that automatically audits a website for these problems, scores it from 0-100, and produces CSV, JSON and HTML reports.

### What the scanner checks

| Module | Checks |
|---|---|
| **Security headers** | HTTPS in use, HSTS, Content-Security-Policy, X-Frame-Options, X-Content-Type-Options, Referrer-Policy, Permissions-Policy, Cross-Origin-Opener-Policy (value quality is validated, not just presence) |
| **Information disclosure** | `Server` version banner, `X-Powered-By` |
| **Cookies** | `Secure`, `HttpOnly`, `SameSite` on every `Set-Cookie` |
| **CORS** | Reflected origin, `null` origin, suffix bypass, prefix bypass, wildcard (`*`), missing `Vary: Origin`, risky pre-flight (`OPTIONS` + `DELETE`) |

### Scoring
Score = 100 - penalties (High = 15, Medium = 8, Low = 3). Grades: A >= 90, B >= 80, C >= 65, D >= 50, F < 50.

### Key results (built-in local test lab)

| Target | Score | Grade | High | Medium | Low |
|---|---|---|---|---|---|
| Lab-Vulnerable | 0 | F | 5 | 5 | 7 |
| Lab-Partial | 59 | D | 1 | 1 | 6 |
| Lab-Hardened | 100 | A | 0 | 0 | 0 |

The hardened server produced **zero false positives**, and all seeded CORS flaws on the vulnerable server were detected
(validated by automated assertions inside the notebook).

## 2. Technologies used

* Python 3.9+
* `requests` - HTTP probing
* `pandas` - result tables and CSV/JSON export
* `matplotlib` - charts
* `http.server` / `threading` (standard library) - local test lab
* Jupyter Notebook

## 3. Dataset

**No external dataset is required.** The scanner collects its own data live from the HTTP responses of the targets.
For reproducibility the notebook starts a **built-in local lab** (three servers on `127.0.0.1`: vulnerable / partial / hardened),
so it runs offline and never touches third-party systems.

Reference material used while designing the checks:
* OWASP Secure Headers Project - https://owasp.org/www-project-secure-headers/
* OWASP Top 10:2021 A05 - https://owasp.org/Top10/A05_2021-Security_Misconfiguration/
* MDN CORS guide - https://developer.mozilla.org/docs/Web/HTTP/CORS
* PortSwigger CORS - https://portswigger.net/web-security/cors

## 4. Setup and run instructions

```bash
# 1. (optional) create a virtual environment
python -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate

# 2. install dependencies
pip install -r requirements.txt

# 3. open the notebook
jupyter notebook Suman_Vyas_WebSecurityScanner.ipynb
```

In Jupyter choose **Kernel -> Restart & Run All**. The run takes about a second and creates an `output/` folder containing:

* `scan_results.csv` - every check and its result
* `scan_results.json` - machine-readable results (SIEM friendly)
* `scan_report.html` - standalone HTML report
* `scan_charts.png`, `category_chart.png` - charts

### Scanning your own site
In the configuration cell, add authorised URLs:
```python
EXTRA_TARGETS = ["https://your-own-site.example"]
```
Set `RUN_LOCAL_LAB = False` to skip the lab.

## 5. Repository contents

| File | Purpose |
|---|---|
| `Suman_Vyas_WebSecurityScanner.ipynb` | Complete project code (executed, with outputs) |
| `requirements.txt` | Python dependencies |
| `Suman_Vyas_ProjectReport.docx` | Full project report |
| `README.md` | This file |

## 6. Ethical use and limitations

* Scan **only** systems you own or have written permission to test.
* The scanner is passive / low-impact (a handful of GET and OPTIONS requests per target).
* It checks the landing page only; it does not crawl, test TLS configuration, or exploit anything.
* Results are indicators for remediation, not proof of exploitability.
