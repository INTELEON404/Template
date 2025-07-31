# 🔍 Automated Recon & Vulnerability Scanning Toolkit (2025 Ultimate Edition)

> **By INTELEON404 | Offensive Security Specialist | Crafted with ❤️ for Web Attack Surface Mastery**

---

## 🧠 What is This?
A powerful, modular, and fully automated **bug bounty & red team recon** system — designed for **precision**, **depth**, and **real-world offensive workflows**. Just plug in a target and **unleash**!

---

## 🧪 Tools Used
```
🔹 subfinder         🔹 katana           🔹 gau             🔹 waybackurls
🔹 httpx             🔹 dalfox           🔹 nuclei          🔹 gf
🔹 sqlmap            🔹 ffuf             🔹 Ghauri          🔹 corsy
🔹 SecretFinder      🔹 GitDorker        🔹 x8              🔹 Custom Payloads
```

---

## ⚙️ Setup Instructions
```bash
git clone https://github.com/yourusername/recon-ultimate.git
cd recon-ultimate
chmod +x recon.sh
./recon.sh example.com
```

🔑 **Requirements:**
- Go Tools installed
- Python3 + pip
- `~/.gf` patterns loaded

---

## 🚀 Workflow Breakdown

### 1. 🔍 Subdomain Enumeration
```bash
subfinder -d example.com -all -o subs.txt
```

### 2. 🌐 Probing Alive Hosts
```bash
httpx -l subs.txt -o alive.txt -title -tech-detect -status-code
```

### 3. 🧬 JS Files + Endpoint Extraction
```bash
katana -list alive.txt -jc -o js_endpoints.txt
waybackurls example.com > wayback.txt
gau example.com >> wayback.txt
sort -u wayback.txt > all_urls.txt
```

### 4. 🧠 GF Pattern Filters (for attack surface)
```bash
cat all_urls.txt | gf xss > xss.txt
cat all_urls.txt | gf sqli > sqli.txt
cat all_urls.txt | gf lfi > lfi.txt
```

### 5. 🧪 SQLi Scanner (Auto)
```bash
gf sqli all_urls.txt | sqlmap -m - --batch --risk=3 --level=5
```

### 6. 🎯 XSS Scanning
```bash
dalfox file xss.txt --custom-payload payloads/xss.txt --output xss_result.txt
```

### 7. 🔐 Secrets Detection
```bash
python3 SecretFinder.py -i js_endpoints.txt -o cli
```

### 8. 🔗 CORS & Host Header Injection
```bash
nuclei -l alive.txt -t cors.yaml,host-header-injection.yaml
```

### 9. 📁 Directory Bruteforce
```bash
ffuf -u https://example.com/FUZZ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -mc 200
```

### 10. 🧪 Final CVE & Misconfig Detection
```bash
nuclei -l alive.txt -c 50 -tags cve,exposures,panels,technologies,misconfiguration
```

---

## 🛠️ Magic Add-ons (Optional)

### 🔎 GitHub Dorking (via GitDorker)
```bash
python3 GitDorker.py -t YOUR_GITHUB_TOKEN -d dorks.txt -q example.com
```

### 🎭 Custom Header Bypass Check
```bash
for url in $(cat alive.txt); do curl -I -H "X-Original-URL: /admin" $url; done
```

### 🧪 x8 Vulnerability Scanner
```bash
x8 -u https://example.com -m all
```

---

## 📁 Output Structure
```
├── subs.txt             # Subdomains
├── alive.txt            # Alive hosts
├── js_endpoints.txt     # JS file links
├── all_urls.txt         # Gau + Wayback
├── xss.txt / sqli.txt   # GF matches
├── xss_result.txt       # Dalfox output
├── secrets.txt          # API keys, tokens
└── nuclei_reports/      # CVE findings
```

---

## 🔥 Pro Tips
- Run in screen/tmux session for stability
- Use VPS + proxychains for stealth
- Customize payloads in `payloads/`

---

## 🧠 Inspiration
> _"Automation without logic is noise. Precision recon is art."_

---

## 🧰 License
MIT — Use responsibly and credit authors ❤️
