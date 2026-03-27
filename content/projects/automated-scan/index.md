---
title: "Passive Recoinaissance Pipeline"
tech: "Python, sh"
description: "Multi-source OSINT pipeline with enrichment, correlation, and risk scoring"
image: "/images/automated-report-project.png"
layout: "simple"
tags: ["python", "html", "css"]
---

<h1>Passive Recon Tool</h1>

<p><strong>TL;DR:</strong> Passive OSINT pipeline that maps a domain’s attack surface without direct interaction, correlates multi-source data, and generates a structured risk-based security report.</p>

{{< figure src="images/report-first-page.png" link="images/report-first-page.png" caption="Report First Page" >}}

I developed a passive reconnaissance pipeline capable of mapping a target domain’s attack surface using only publicly available data (OSINT).

Unlike traditional scanning approaches, this tool performs <strong>zero direct interaction with the target</strong>, eliminating detection risks while still providing meaningful security insights.

The system aggregates data from multiple external sources, correlates findings, and transforms fragmented information into a structured and actionable security report.

This project simulates a real-world reconnaissance phase used in penetration testing and attack surface analysis, focusing on scalability, automation, and low-noise information gathering.

This approach ensures:

* No direct contact with the target
* No legal or ethical risks
* Real-world applicability in reconnaissance phases

<h2>Technical Decisions</h2>

Several design choices were made to ensure reliability, scalability, and data consistency:

- <strong>VirusTotal (Passive DNS):</strong>  
  Selected for historical DNS resolution data, allowing timeline-based infrastructure analysis and identification of IP reuse.  
  The implementation deduplicates records and tracks first/last seen timestamps.

- <strong>crt.sh (Certificate Transparency):</strong>  
  Used to enumerate subdomains from SSL certificates, enabling discovery of hidden or legacy assets often missed by DNS-based methods.  
  Wildcard certificates are handled separately to avoid noise.

- <strong>Threaded Resolution (Concurrency):</strong>  
  Subdomain and infrastructure validation use multithreading to significantly reduce execution time.

- <strong>Data Normalization Layer:</strong>  
  WHOIS data is normalized into a structured domain model to handle inconsistent formats across registrars.

- <strong>Deduplication Strategy:</strong>  
  DNS records, certificates, and subdomains are aggregated using key-based correlation to avoid redundant entries.

- <strong>Resilience Handling:</strong>  
  API failures and timeouts are handled, ensuring the pipeline continues execution even with partial data.

- <strong>Modular Architecture:</strong>  
  Each stage (collection, processing, enrichment, reporting) is isolated, enabling easy extension and maintenance.

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

{{< figure src="images/diagram.png" link="images/diagram.png" caption="Pipeline Diagram" >}}

```
                
[Input Layer]
    └── Target Domain

[Collection Layer]
    ├── WHOIS Collection
    ├── Passive DNS (VirusTotal API)
    ├── Certificate Transparency (crt.sh)
    └── Wayback Machine (Document Discovery)

[Processing Layer]
    ├── Data Normalization (WHOIS parsing)
    ├── Deduplication (DNS, certs, subs)
    ├── Timeline Correlation (first_seen / last_seen)
    └── Metadata Classification

[Enrichment Layer]
    ├── IP Intelligence (IPInfo API)
    └── Active/Inactive Resolution (Threaded DNS lookup)

[Analysis Layer]
    ├── Subdomain Risk Detection (keyword-based)
    ├── Metadata Exposure Analysis
    ├── Infrastructure Sizing
    └── Domain Lifecycle Analysis

[Scoring Engine]
    └── Weighted Risk Model (Low/Medium/High → Score → Average)

[Output Layer]
    └── HTML Report Generator
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


<h2>Risk Assessment Model</h2>

The tool implements a heuristic-based risk scoring system that evaluates multiple aspects of the target:

- <strong>Domain Management:</strong>  
  Based on expiration proximity and WHOIS completeness

- <strong>Subdomain Exposure:<strong>  
  Detection of sensitive naming patterns such as dev, test, admin, and internal

- <strong>Metadata Exposure:</strong>  
  Volume of publicly accessible documents and potential information leakage

- <strong>Infrastructure Exposure:</strong>
  Size and distribution of the identified infrastructure

{{< figure src="images/risk-assessment.png" link="images/risk-assessment.png" caption="Risk Assessment Section" >}}

Each category is classified as <strong>Low, Medium, or High</strong>, and assigned a numerical score:

* Low = 1  
* Medium = 2  
* High = 3  

The overall risk is calculated using an average-based scoring model:

* ≥ 2.5 → High  
* ≥ 1.5 → Medium  
* < 1.5 → Low  

This approach provides a simple but effective way to prioritize potential risks based on aggregated indicators.

<h2>Results</h2>

The tool successfully automates the reconnaissance workflow and produces a structured security report in a short time frame.

Example outcomes include:

- Identification of multiple subdomains through certificate transparency logs
- Detection of active vs inactive assets via DNS resolution
- Discovery of publicly accessible documents from archived sources
- Correlation of infrastructure data (ASN, organization, geolocation)
- Automated classification of risk across multiple categories

Execution is optimized through concurrency, allowing multiple enrichment and validation tasks to run in parallel, significantly reducing total runtime.

The final output is a comprehensive HTML report that consolidates all findings into a readable and actionable format.

<h2>Limitations</h2>

While effective, the tool has some limitations:

- Relies on third-party APIs, which may introduce rate limits or incomplete data
- Passive-only approach may miss assets not exposed through public sources
- WHOIS data inconsistency across registrars can affect accuracy
- Risk scoring is heuristic-based and does not replace manual analysis

These limitations reflect real-world constraints of passive reconnaissance techniques.

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

This project demonstrates how publicly available data can be systematically leveraged to map a target’s attack surface without active interaction.

Beyond data collection, the focus was on building a structured pipeline capable of:

- Correlating multiple OSINT sources
- Reducing noise through normalization and deduplication
- Automating analysis and reporting
- Providing actionable security insights

This work reflects my ability to design scalable reconnaissance workflows and think in terms of attack surface analysis, a critical component in modern cybersecurity operations.


<h2>Repository</h2>


<a href="https://github.com/gabsteps/automated_report_scan" class="link"><i class="fas fa-link"></i> GitHub Repository Link Here 🔗</a></li>




<h2>Author</h2>

<h3>Gabriel Passos
Cybersecurity Student & Security Enthusiast</h3>
