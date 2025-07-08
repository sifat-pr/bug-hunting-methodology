# 📊 Bug Bounty Recon and Exploitation Cheat Sheet

---

## 1. 🔎 Sudomy Command (Subdomain Enumeration & Web Analysis)

```bash
sudomy -d target -dP -eP -rS -cF -pS -tO -gW --httpx -aI webanalyze
```

* `-d`: Target domain
* `-dP`, `-eP`, `-rS`, `-cF`, `-pS`, `-tO`, `-gW`: Subdomain & metadata collection
* `--httpx`: Probe discovered domains
* `-aI webanalyze`: Analyze web technologies

---

## 2. 🕵️ PDF Link Hijacking & JS Secrets

* **Broken PDF links (via Wayback):**

  * Extract old PDFs and check for hijackable subdomains
* **JS Secrets:**

  * Use Burp to inspect embedded secrets
  * Visit: [https://googlekey.blindf.com/](https://googlekey.blindf.com/) for live testing

---

## 3. 📂 Waybackurls + Grepping Sensitive Info

```bash
waybackurls target.com | grep -Ei 'user|api|token|@|redirect=|logout=|admin|info|number|address|firstname|lastname'
```

### 🧬 IDOR Discovery

```bash
cat Passive_Collect_URL_Full.txt | egrep "id=|uid=|gid=|user_id=|user=|account=|order=|doc=|file=|email=|edit=|report="
```

### ➡️ Open Redirect Testing

```bash
cat Passive_Collect_URL_Full.txt | grep -ai '=http' | bhedak 'http://redirect.com' | while read host; do
  curl -s -L "$host" -I | grep "redirect.com" && echo -e "$host \033[0;31mVulnerable\n";
done
```

### 💡 XSS Testing

```bash
cat Passive_Collect_URL_Full.txt |
  grep '=' |
  egrep -iv '.(jpg|jpeg|gif|css|png|ico|pdf|svg|js)' |
  uro |
  gsreplace '><img src=x onerror=alert(1);>' |
  freq
```

### ⛔ 403 Bypass Tips

* Add headers: `X-Original-URL`, `X-Forwarded-For`, etc.
* Change request method to `OPTIONS`, `POST`, `HEAD`
* Append `/..;/`, `/index.php/` to bypass filters

---

## 4. 🛁 Port Scanning & Discovery

```bash
naabu -il ip_resolver.txt -p 21,22,2075,2076,6443,3868,3366,8443,8080,9443,9091,3000,8000,5900,8081,6000,10000,8181,3306,5000,4000,8888,5432,15672,9999,161,4044,7077,4040,9000,8089,443,7447,7080,8880,8983,5673,7443,19000,19080 | tee bbcportscan.txt
```

* Check special services: MySQL (3306), FTP (21), Redis, Jenkins, etc.

---

## 5. 🛠️ Uncover + Nuclei + Dirsearch

```bash
uncover -q "target.com" -e censys,fofa,shodan | httpx | tee ips.txt
```

* Analyze for exposed services, CDN bypass, origin leaks

### 📁 Dirsearch Bruteforce

```bash
python3 dirsearch.py -e php,asp,aspx,jsp,py,txt,conf,config,bak,backup,swp,old,db,sql,zip,tar.gz,rb,cache,cgi,csv,html,inc,jar,js,json,log,rar,wadl,bkp,bz2 -u https://ruba.com -r -b --json-report=dirsearch.json
```

---

## 6. 🔍 GitHub Recon

* Visit: [https://vsec7.github.io/](https://vsec7.github.io/)
* GitHub Dorks: [github\_dorks.txt](https://raw.githubusercontent.com/7srambo/bugbounty/main/github_dorks.txt)

---

## 7. 🧪 Web Technology Exploits

### 🤖 AngularJS SSTI

* SSTI Cheatsheet: [HackTricks SSTI](https://book.hacktricks.xyz/pentesting-web/ssti-server-side-template-injection)

### 💻 WordPress Testing

```bash
wpscan --url https://target.com/ --api-token YOUR_API_KEY
```

* Identify outdated plugins/themes, exposed users, etc.

---

## ✅ Final Advice

* Use these chains in real-world bounty hunting
* Tailor keyword lists, payloads, and tools based on target stack
* Practice legally, ethically, and responsibly

---

Happy Hacking! 🚀
