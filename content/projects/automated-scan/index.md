---
title: "Automated Scan"
tech: "Python, sh"
description: "Automated tool to scan targets and generate reports."
image: "/images/automated-report-project.png"
layout: "simple"
tags: ["python", "html", "css"]
---

<h1>Passive Recon Tool</h1>

{{< figure src="images/report-first-page.png" link="images/report-first-page.png" caption="Report First Page" >}}

In this project, I developed a **passive reconnaissance tool** designed to collect, process, and analyze publicly available data (OSINT) from a target domain.

The goal is to transform scattered public information into a **structured security report**, helping identify potential risks without performing any active interaction with the target.

This approach ensures:

* No direct contact with the target
* No legal or ethical risks
* Real-world applicability in reconnaissance phases


<h2>Objectives</h2>

This project was built with the following goals:

* Automate passive reconnaissance workflows
* Aggregate multiple OSINT sources into a single pipeline
* Identify security-relevant patterns in public data
* Generate a clean and readable report for analysis
* Simulate a real-world reconnaissance phase used in pentesting


<h2>Key Concepts</h2>

This project explores important cybersecurity concepts:

* Passive Reconnaissance (OSINT)
* Attack Surface Mapping
* Certificate Transparency
* Metadata Exposure
* Infrastructure Enumeration
* Risk Assessment Modeling


<h2>Architecture</h2>

The tool follows a modular pipeline:

```text
Target Domain
     ↓
WHOIS Collection
     ↓
Passive DNS (VirusTotal)
     ↓
Subdomain Enumeration (crt.sh)
     ↓
Infrastructure Enrichment (IPInfo)
     ↓
Document Discovery (Wayback Machine)
     ↓
Metadata Analysis
     ↓
Risk Assessment
     ↓
HTML Report Generation
```

<h2>External APIs Used</h2>
<ul>
  <li><a href="https://www.virustotal.com/" class="link"><i class="fas fa-link"></i> VirusTotal 🔗</a></li>
  <li><a href="https://crt.sh/" class="link"><i class="fas fa-link"></i> crt.sh 🔗</a></li>
  <li><a href="https://ipinfo.io" class="link"><i class="fas fa-link"></i> IPInfo 🔗</a></li>
  <li><a href="https://web.archive.org/" class="link"><i class="fas fa-link"></i> Wayback Machine 🔗</a></li>
</ul>


<h2>Project Structure</h2>

```text
.
├── recon.py             # Main execution script
├── passive.py           # OSINT data collection
├── data_filter.py       # WHOIS data processing
├── domain.py            # Domain data model
├── pdfgenerator.py      # Report generation
├── requirements.txt
└── template/
    └── report_template_passive_css.html
```


<h2>Data Collection Techniques</h2>

<h3>1. WHOIS Analysis</h3>

Used to extract:

* Registrar information
* Domain lifecycle dates
* Organization details

{{< figure src="images/whois-info.png" link="images/whois-info.png" caption="Whois Section" >}}

<strong>Security insight:</strong>
Missing or obfuscated data may indicate privacy protection or misconfiguration.


<h2>2. Passive DNS</h2>

Collected via VirusTotal API:

* Historical IP resolutions
* Infrastructure mapping

**Security insight:**
Helps identify infrastructure changes and potential shared hosting risks.

{{< figure src="images/dns-records.png" link="images/dns-records.png" caption="DNS Records Section" >}}


<h2>3. Subdomain Enumeration</h2>

Using certificate transparency logs (crt.sh):

* Discovers hidden or forgotten subdomains

{{< figure src="images/identified-subs.png" link="images/identified-subs.png" caption="Identified Subs Section" >}}

<strong>Security insight:</strong>
Subdomains like `dev`, `test`, or `admin` may expose sensitive environments.

Analyzing the collected data reveals domain reuse of gabrielpassos.com through the observed dates.


<h2>4. Infrastructure Analysis</h2>

IP enrichment using external APIs:

* ASN
* Organization
* Country

{{< figure src="images/identified-ips.png" link="images/identified-ips.png" caption="Identified IP's Section" >}}

<strong>Security insight:</strong>
Expands the attack surface visibility.


<h2>5. Public Document Discovery</h2>

Wayback Machine is used to find:

* PDFs
* DOC/DOCX files

<strong>Security insight:</strong>
Documents may expose:

* Internal usernames
* Software versions
* Sensitive metadata


<h2>Risk Assessment</h2>

The tool includes a simple risk model based on:

* Domain expiration proximity
* Suspicious subdomain naming
* Volume of exposed documents
* Infrastructure size

{{< figure src="images/risk-assessment.png" link="images/risk-assessment.png" caption="Risk Assessment Section" >}}

Each category is classified as:

* Low
* Medium
* High

And combined into an <strong>overall risk score.</strong>


<h2>Output</h2>

The final output is an HTML report containing:

* WHOIS data
* DNS records
* Subdomains (active/inactive)
* Certificates
* Infrastructure details
* Metadata findings
* Risk assessment


<a href="/downloads/report.html" download class="link">Download HTML Report Example 🔗</a>

<h2>Key Challenges</h2>

During development, some challenges included:

* Handling inconsistent WHOIS formats
* Normalizing data from multiple APIs
* Avoiding duplicate records
* Managing API failures and timeouts
* Designing a clean and readable report


<h2>What I Learned</h2>

This project helped me improve:

* OSINT data correlation
* Secure data handling
* Writing modular Python code
* Designing analysis pipelines
* Thinking like an attacker during recon


<h2>Future Improvements</h2>

Planned enhancements:

* Integration with additional OSINT sources
* PDF export support
* Improved risk scoring model
* Web-based interface
* Caching for faster execution


<h2>Ethical Considerations</h2>

This tool operates strictly in a <strong>passive manner</strong>, meaning:

* No direct interaction with the target
* No scanning or exploitation
* Only publicly available data is used


<h2>Conclusion</h2>

This project demonstrates how publicly available data can be leveraged to gain meaningful insights into a target's security posture.

Even without active interaction, it is possible to:

* Map infrastructure
* Identify exposure points
* Assess potential risks


<h2>Repository</h2>


<a href="https://github.com/gabsteps/automated_report_scan" class="link"><i class="fas fa-link"></i> GitHub Repository Link Here 🔗</a></li>




<h2>Author</h2>

<h3>Gabriel Passos
Cybersecurity Student & Security Enthusiast</h3>
