**Bug Bounty Recon and Exploitation Cheat Sheet**

---

### 1. Sudomy Command (Subdomain Enumeration & Web Analysis)
```bash
sudomy -d target -dP -eP -rS -cF -pS -tO -gW --httpx -aI webanalyze
```
- `-d`: Target domain
- `-dP`, `-eP`, `-rS`, `-cF`, `-pS`, `-tO`, `-gW`: Subdomain and metadata collection
- `--httpx`: Send results through httpx for probing
- `-aI webanalyze`: Analyze technologies used on web targets

---

### 2. PDF Link Hijacking & Secrets from JS
- **Broken Link Hijacking (PDFs via Wayback):**
  Use waybackurls and check if old PDFs contain broken links pointing to hijackable domains.

- **Secrets from JS (Using BurpSuite):**
  Visit: [https://googlekey.blindf.com/](https://googlekey.blindf.com/) to test for hardcoded secrets.

---

### 3. Waybackurls + Sensitive Info Grepping
```bash
waybackurls target.com | grep -Ei 'user|api|token|@|redirect=|logout=|admin|info|number|address|firstname|lastname'
```

`Try to find IDOR`
```bash
cat Passive_Collect_URL_Full.txt | egrep "id=|uid=|gid=|user_id=|user=|account=|number=|order=|no=|doc=|file=|key=|email=|group=|profile=|edit=|report="
```

- Add more keywords based on target.

#### Open Redirect Testing:
```bash
cat Passive_Collect_URL_Full.txt | grep -ai '=http' | bhedak 'http://redirect.com' | while read host; do
  curl -s -L "$host" -I | grep "redirect.com" && echo -e "$host \033[0;31mVulnerable\n";
done
```

#### XSS Testing:
```bash
cat Passive_Collect_URL_Full.txt |
  grep '=' |
  egrep -iv '.(jpg|jpeg|gif|css|tif|tiff|png|ttf|woff|woff2|ico|pdf|svg|txt|js|php|py)' |
  uro |
  gsreplace '><img src=x onerror=alert(1);>' |
  freq
```

#### 403 Bypass:
- Try accessing via direct paths or include authorization tokens manually.

---

### 4. Port Scanning and Vulnerability Discovery
```bash
naabu -il ip_resolver.txt -p 21,22,2075,2076,6443,3868,3366,8443,8080,9443,9091,3000,8000,5900,8081,6000,10000,8181,3306,5000,4000,8888,5432,15672,9999,161,4044,7077,4040,9000,8089,443,7447,7080,8880,8983,5673,7443,19000,19080 | tee bbcportscan.txt
```
- **Special Ports**: 21 (Check anonymous FTP), 3306 (MySQL), 3806, etc.

---

### 5. Uncover + Nuclei + Dirsearch
```bash
uncover -q "target.com" -e censys,fofa,shodan,shodan-idb | httpx | tee ips.txt
```
- Analyze IPs for origin leaks / cloudflare bypass.
- Run `nuclei` and `dirsearch` on discovered hosts.

#### Bruteforce with Dirsearch:
```bash
python3 dirsearch.py -e php,asp,aspx,jsp,py,txt,conf,config,bak,backup,swp,old,db,sql,zip,tar.gz,rb,cache,cgi,csv,html,inc,jar,js,json,log,rar,wadl,bkp,bz2 -u https://ruba.com -r -b --json-report=dirsearch.json
```

---

### 6. GitHub Recon
- Visit: [https://vsec7.github.io/](https://vsec7.github.io/)
- GitHub Dorks: [https://raw.githubusercontent.com/7srambo/bugbounty/main/github_dorks.txt](https://raw.githubusercontent.com/7srambo/bugbounty/main/github_dorks.txt)

---

### 7. Web Technology Analysis & Exploits
- **AngularJS**:
  - Check for SSTI: [SSTI Cheatsheet](https://book.hacktricks.xyz/pentesting-web/ssti-server-side-template-injection)

- **WordPress**:
  - Info: [WordPress Pentesting](https://book.hacktricks.xyz/network-services-pentesting/pentesting-web/wordpress)
  - Scan: `wpscan --url https://target.com/ --api-token XXXXXX`

---

Stay safe and ethical. Hack the planet responsibly!

