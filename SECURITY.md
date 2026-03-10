# Security Assessment Report: Craft CMS 5 Installation

I have performed a security assessment of the Craft CMS 5.9.15 installation and identified several critical vulnerabilities related to environment configuration.

## Summary of Findings

| Vulnerability | Severity | Status | PoC Included |
| :--- | :--- | :--- | :--- |
| **Sensitive Information Disclosure (.env Exposure)** | **Critical** | Vulnerable | Yes |
| **Directory Listing Enabled** | **High** | Vulnerable | Yes |
| **Weak Database Configuration** | **Medium** | Vulnerable | Yes |
| **Known CVEs (RCE, SSTI)** | **Low** | Patched | N/A |

---

## 1. Sensitive Information Disclosure (.env Exposure)
The [.env](file:///e:/app/xammp/htdocs/navee/craft-site/.env) file, which contains secrets like the `CRAFT_SECURITY_KEY` and database credentials, is publicly accessible via the web browser.

### Proof of Concept (PoC)
Navigate to: `http://localhost:8080/navee/craft-site/.env`

**Observed Result**: The browser displays the full content of the [.env](file:///e:/app/xammp/htdocs/navee/craft-site/.env) file, including:
- `CRAFT_SECURITY_KEY=IOmfQhLSBXOH4xsoNEc6uEsSAA3eXicY`
- `CRAFT_DB_USER=root`
- `CRAFT_DB_PASSWORD=`

### Impact
An attacker can gain full access to the database and potentially achieve Remote Code Execution (RCE) by leveraging the leaked security key to sign malicious payloads.

---

## 2. Directory Listing Enabled
The web server allows anyone to browse the file structure of the project.

### Proof of Concept (PoC)
Navigate to: `http://localhost:8080/navee/craft-site/`

**Observed Result**: An index page is shown listing all files and folders in the project root.

### Impact
Reveals the application's structure, installed packages, and configuration files, making it easier for an attacker to find other vulnerabilities.

---

## 3. Weak Database Configuration
The database is using the `root` user with no password.

### Proof of Concept (PoC)
Check [.env](file:///e:/app/xammp/htdocs/navee/craft-site/.env):
```dotenv
CRAFT_DB_USER=root
CRAFT_DB_PASSWORD=
```

### Impact
If the database port (3306) is exposed or if a local exploit is found, an attacker has immediate full control over the database.

---

## Recommendations
1.  **Correct Web Root**: Change your Apache configuration so the `DocumentRoot` points directly to the `web/` directory:
    - Path: `e:\app\xammp\htdocs\navee\craft-site\web`
2.  **Disable Directory Listing**: In [httpd.conf](file:///e:/app/xammp/apache/conf/httpd.conf) or a `.htaccess` file, add:
    ```apache
    Options -Indexes
    ```
3.  **Secure Database**: Set a strong password for the MySQL `root` user and update the [.env](file:///e:/app/xammp/htdocs/navee/craft-site/.env) file.
4.  **Production Settings**: When going live, set `CRAFT_DEV_MODE=false` and `CRAFT_ALLOW_ADMIN_CHANGES=false`.

![PoC Screening](/C:/Users/Admin/.gemini/antigravity/brain/69bd3476-1c5f-4294-9a73-ad29f9e9f555/security_testing_env_exposure_1773173902571.webp)
