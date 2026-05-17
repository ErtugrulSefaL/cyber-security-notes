---
Main Topic: Tools
Sub Topic: OWASP_ZAP_Introdiction
Links:
  - https://tryhackme.com/room/learnowaspzap
Date: 25-03-2026
Related Notes:
Tags:
---

# OWASP_ZAP_Introdiction

What are the benefits to OWASP ZAP?

It’s completely open source and free. There is no premium version, no features are locked behind a paywall, and there is no proprietary code.  

There’s a couple of feature benefits too with using OWASP ZAP over Burp Suite:

- Automated Web Application Scan: This will automatically passively and actively scan a web application, build a sitemap, and discover vulnerabilities. This is a paid feature in Burp.
- Web Spidering: You can passively build a website map with Spidering. This is a paid feature in Burp.
- Unthrottled Intruder: You can bruteforce login pages within OWASP as fast as your machine and the web-server can handle. This is a paid feature in Burp.
- No need to forward individual requests through Burp: When doing manual attacks, having to change windows to send a request through the browser, and then forward in burp, can be tedious. OWASP handles both and you can just browse the site and OWASP will intercept automatically. This is NOT a feature in Burp.

---

> [!faq] What is DVWA
> **Damn Vulnerable Web Application**  
> This is a PHP/MySQL web application that is damn vulnerable. Its main goal is to be an aid for security professionals to test their skills and tools in a legal environment, help web developers better understand the processes of securing web applications and to aid both students & teachers to learn about web application security in a controlled class room environment.
>
> The aim of DVWA is to practice some of the most common web vulnerabilities, with various levels of difficulty, with a simple straightforward interface. Please note, there are both documented and undocumented vulnerabilities with this software. This is intentional. You are encouraged to try and discover as many issues as possible.
> https://www.kali.org/tools/dvwa/

---

## Automated Scan
Lets perform an automated scan. Click the big Automated Scan button and input your target.

The automated scan performs both passive and automated scans to build a sitemap and detect vulnerabilities.

On the next page you may see the options to select either to use “traditional spider” or “Ajax spider”.  

A traditional spider scan is a passive scan that enumerates links and directories of the website. It builds a website index without brute-forcing. This is much quieter than a brute-force attack and can still net a login page or other juicy details, but is not as comprehensive as a bruteforce.  

The Ajax Spider is an add-on that integrates in ZAP a crawler of AJAX rich sites Lets perform an automated scan. Click the big Automated Scan button and input your target.called Crawljax. You can use it in conjunction with the traditional spider for better results. It uses your web browser and proxy.  Lets perform an automated scan. Click the big Automated Scan button and input your target.
Lets perform an automated scan. Click the big Automated Scan button and input your target.
The easiest way to use the Ajax Spider is with HTMLUnit.

> [!FAQ] What is HTML Unit? 
> HtmlUnit is a "GUI-Less browser for Java programs". It models HTML documents and provides an API that allows you to invoke pages, fill out forms, click links, etc... just like you do in your "normal" browser. It has fairly good JavaScript support (which is constantly improving) and is able to work even with quite complex AJAX libraries, simulating Chrome, Firefox or Edge depending on the configuration used. It is typically used for testing purposes or to retrieve information from websites.
> https://htmlunit.sourceforge.io/
