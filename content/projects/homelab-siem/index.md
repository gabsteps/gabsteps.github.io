---
title: "Homelab SOC: Active Directory + File Server + Wazuh SIEM"
tech: ""
description: "Homelab with AD authentication, SMB file sharing, a Flask web app, and Wazuh security monitoring running in Docker."
image: "/images/Wazuh-With-Docker.png"
layout: "simple"
tags: ["AD", "LDAP", "Flask", "Wazuh", "Docker"]
---


<article>

<header>

<h2>Homelab SOC: Active Directory, File Server & Wazuh SIEM</h2>

<strong> a realistic enterprise security lab to simulate identity, file access and endpoint monitoring.</strong>

This project is a continuously evolving <strong>Security Operations (SOC) homelab</strong> designed to replicate a small corporate infrastructure and practice real-world detection, monitoring and troubleshooting scenarios.

</header>


<section id="why">

<h2>Why This Project Exists</h2>

Most labs focus on isolated tools. Real environments don’t.

This project was created to practice <strong>how systems interact</strong>, where real security problems appear:

- Authentication flows  
- Log centralization  
- Monitoring endpoints and servers  
- Troubleshooting integrations  
- Hardening services over time  

The goal is not just to build a lab — but to <strong>operate it like a real environment</strong>.

</section>


<section id="architecture">

<h2>High-Level Architecture</h2>

This lab simulates a small company internal network where users authenticate, access shared files and use an internal web portal — all monitored by a SIEM.

<figure>
    {{< figure src="/projects/homelab-siem/images/homelab-diagram.png" caption="Homelab SOC architecture diagram" >}}
</figure>


Core components:

- Active Directory domain  
- SMB file server with NTFS permissions  
- Internal Flask web application (AD authentication)  
- Wazuh SIEM (Manager + Indexer + Dashboard)  
- Wazuh agents across all hosts and containers  

</section>


<section id="overview">

<h2>Environment </h2>

This environment represents a realistic enterprise setup:

<figure>
    {{< figure src="/projects/homelab-siem/images/dockerps.png" caption="Applications On Docker" >}}
</figure>


- Identity Provider: Active Directory domain controller       
- File Services: SMB file server with departmental shares 
- Internal App: Flask portal integrated with LDAP & SMB  
- Security Monitoring: Wazuh SIEM stack                         
- Endpoint Monitoring: Agents on servers and containers     
  

</section>


<section id="features">

<h2>Core Features</h2>

<h3>Identity & Access<h3>

<figure>
    {{< figure src="/projects/homelab-siem/images/adusersandcomputers.png" link="/projects/homelab-siem/images/adusersandcomputers.png" caption="Active Directory" >}}
</figure>

- Centralized authentication via <strong>LDAP / Active</strong>
- Domain users and groups controlling file access
- Group Policies simulating enterprise restrictions

<h3>File Server Monitoring</h3>

- SMB access monitoring  
- File creation, deletion and modification tracking  
- Detection of unauthorized access attempts  

<h3>Web Application Integration</h3>

Custom internal portal that:

<figure>
    {{< figure src="/projects/homelab-siem/images/webappdp.png" link="/projects/homelab-siem/images/webappdp.png" caption="Web App Directories" >}}
</figure>

<figure>
    {{< figure src="/projects/homelab-siem/images/webappit.png" link="/projects/homelab-siem/images/webappdp.png" caption="Web App Directories" >}}
</figure>

- Authenticates users against Active Directory  
- Lists SMB shared files  
- Allows directory navigation  
- Supports secure file downloads  

This simulates a real <strong>intranet-style corporate application</strong>.

<h3>SIEM Visibility</h3>

Full <strong>Wazuh monitoring</strong> across the environment:

<figure>
    {{< figure src="/projects/homelab-siem/images/wazuhpanel.png" link="/projects/homelab-siem/images/webappdp.png" caption="Wazuh Dashboard" >}}
</figure>

- Authentication and security logs from Active Directory  
- File integrity and system activity monitoring  
- Container and host-level log collection  
- Centralized alerting and visibility

</section>



<section id="detections">

<h2>Detection & Monitoring Use Cases</h3>

The lab already supports detection of:

<figure>
    {{< figure src="/projects/homelab-siem/images/wazuhendpoints.png" link="/projects/homelab-siem/images/webappdp.png" caption="Wazuh Endpoints" >}}
</figure>

- Failed logon attempts in Active Directory  
- Brute-force and repeated authentication failures
- Suspicious system and container activity  
- File integrity and system changes  
- Container lifecycle events 

This provides a foundation to practice <strong>SOC workflows and detection engineering</strong>.

</section>



<section id="security">

<h2>Current Security Posture</h2>

<h3>Implemented</h3>

- Centralized authentication (LDAP)  
- Permission-based file access  
- Full SIEM monitoring across hosts and containers  

<h3>Planned Hardening</h3>

- HTTPS for the web application  
- Remove credential storage from session  
- Container hardening  
- Custom Wazuh detection rules  
- More granular File Integrity Monitoring  

</section>


<section id="roadmap">

<h2>Project Roadmap</h2>

Future improvements include:

- Detection engineering scenarios  
- Simulated attack techniques  
- Custom Wazuh rules and dashboards  
- Security automation experiments  
- Zero-trust concepts  

</section>



<section id="changelog">

<h2>Latest Updates</h2>

<strong>2026-04 — Major SIEM Integration</strong>

- Wazuh agents deployed across all hosts  
- Web application onboarded as monitored endpoint  
- Centralized logging fully operational  
- Improved Docker persistence strategy  

</section>



<section id="stack">

<h2>Technical Stack</h2>

<h3>Infrastructure</h3>

- Active Directory  
- Windows Server  
- Linux Servers  
- Docker  

<h3>Security</h3>

- Wazuh SIEM  
- OpenSearch  
- Endpoint monitoring  
- Log analysis  

<h3>Development</h3>

- Python  
- Flask  
- LDAP (ldap3)  
- SMB (Impacket)  

</section>



<section id="articles">

<h2>Related Technical Articles</h2>

This project acts as the <strong>central hub</strong> for all technical deep dives.

<nav>
<ul>
<li><a href="/articles/deepdive-homelab-webpanel/">WEB Panel integration with Active Directory 🔗</a></li>
</ul>
</nav>

</section>



<footer>

Project status: <strong>Actively maintained and continuously evolving.</strong>

</footer>

</article>