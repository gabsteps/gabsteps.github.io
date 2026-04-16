---
title: "Integrating a Web Panel with Active Directory and a File Server"
date: 2026-04-16
summary: "A deep dive into my homelab SIEM project"
layout: "single"
tags: ["LDAP", "SMB", "Flask", "Homelab", "Security Engineering", "Deep Dive"]
---

<article>

<header>

<h1>Integrating a Web Panel with Active Directory and a File Server</h1>

<h2>Challenges, Lessons Learned and Real-World Troubleshooting</h2>

One of the core goals of my homelab SOC project was to simulate a realistic corporate workflow where users authenticate using Active Directory and access shared files through an internal web portal.

This article is a deep dive into <strong>challenges</strong> faced while building the ADPanel — a Flask web application that authenticates users via LDAP and allows them to browse and download files from an SMB File Server.

This turned out to be far more complex than writing the application
 
</header>

<section id="goal">

<h2>The Goal</h2>

The initial idea was simple:

Build an internal web portal that allows domain users to:

- Log in using Active Directory credentials  
- Browse corporate file shares  
- Download files they are authorized to access  

Architecture goal:

{{< figure src="images/webpaneldiag.png" caption="Application Diagram" >}}

User → Web App → Active Directory (LDAP)  
User → Web App → File Server (SMB)

This sounds straightforward — until you try to integrate all components.

</section>

<section id="challenge">

<h2>The Real Challenge: </h2>

The main difficulty of this project was not coding the web app, but <strong>making multiple enterprise technologies work together</strong>:

- LDAP authentication (Active Directory)
- SMB file access (Windows File Server)
- Python Flask web application
- Docker containerization
- Network isolation

Each technology uses <strong>different protocols, authentication models and networking assumptions</strong>.



<h3>Challenge 1 — Multi-Protocol Authentication Flow</h3>

The first major hurdle was understanding that:

LDAP authentication ≠ SMB authentication.

Even though both rely on Active Directory, they are <strong>completely independent sessions</strong>.

</section>

<section id="problem">

<h2>The Problem</h2>

The application was able to authenticate users successfully against Active Directory.  
A simple LDAP bind confirmed that credentials were valid and the login flow worked as expected.

At this point, authentication seemed solved.


{{< figure src="images/auth-ad.png" link="images/auth-ad.png" caption="AD Auth Code Block" >}}


The application performs a direct LDAP bind using the user credentials.
If the bind succeeds, the identity is validated by Active Directory.

However, immediately after login, the application still could not access the SMB file shares.

This was confusing at first:
If the credentials were valid, why couldn't the app access the files?

The answer lies in how enterprise protocols work.

LDAP only validates identity.
SMB requires its own authenticated session.

This meant the application actually needed to authenticate twice:

Once this became clear, the workflow had to change.
After validating the user via LDAP, the application needed to establish a second authenticated session, this time with the File Server.

{{< figure src="images/auth-smb.png" link="images/auth-smb.png" caption="SMB Auth Code Block" >}}

This realization was a turning point in the project and reshaped the entire authentication workflow.

</section>

<section id="solution">

<h2>Solution</h2>

The final workflow became:

1. User logs in using LDAP bind  
2. Credentials stored temporarily in session  
3. SMB connection established using same credentials  
4. File Server enforces NTFS permissions  

This aligned authentication and authorization with real enterprise behavior.

</section>

<section id="challenge2">

<h2>Challenge 2 — Network Isolation and Connectivity</h2>

This was one of the most critical problems.

The lab environment contained:

{{< figure src="images/networkdiag.png" caption="Network Diagram" >}}

- Active Directory server in an isolated network
- File Server in the same lab network
- Docker container running in a different network

</section>

<section id="symptoms">

<h2>Symptoms</h2>

- LDAP connection failures  
- SMB connection failures  
- DNS resolution issues  
- Containers unable to reach domain services  

At first, the application appeared broken — but the issue was <strong>network architecture</strong>, not code.

</section>

<section id="cause">

<h2>Root Cause</h2>

Containers do not automatically inherit host network routes or domain DNS configuration.

From the container’s perspective:

The domain controllers simply did not exist.

</section>

<section id="solution2">

<h2>Solution</h2>

Several networking adjustments were required:

- Adjust Docker networking configuration  
- Ensure container could reach lab subnet  
- Configure routing and connectivity between environments  

This step transformed the project from a simple web app into a <strong>real infrastructure exercise</strong>.

</section>

<section id="challenge3">

<h2>Challenge 3 — Containerization and Debugging in Docker</h3>

Migrating the app to Docker improved portability but introduced new complexity.


<h3>Problems Encountered</h3>

- Missing Python dependencies  
- Broken runtime environment  
- Harder debugging due to container isolation  

<h3>A new error appeared</h3>

<strong>ModuleNotFoundError: No module named 'flask'</strong>


This revealed that the environment was not reproducible.

</section>

<section id="solution3">

<h2>Solution</h2>

The project evolved to include:

- Proper Dockerfile  
- requirements.txt  
- Fully reproducible build environment  

This eliminated environment drift and standardized 

</section>

<section id="challenge4">

<h2>Challenge 4 — Making Directory Navigation Work with SMB</h2>

SMB integration proved to be one of the most technical parts of the project.

The application must manually handle:

- SMB connection  
- Session creation  
- Tree connections  
- Directory listing  
- File downloads  

This is significantly more complex than typical web development tasks.

Working with SMB introduced challenges such as:

- Error handling complexity  
- NTFS compatibility considerations  
- Understanding enterprise file access 

Directory enumeration relied on the SMB `listPath` operation:

{{< figure src="images/listpath.png" link="images/listpath.png" caption="ListPath Code Block" >}}

This step moved the project closer to real infrastructure environment.

</section>

<section id="challenge5">

<h2>Challenge 5 — Preparing for SIEM Integration</h2>

As the lab evolved, observability became a new requirement.

Questions began to emerge:

- What events should be logged?
- How should authentication events be recorded?
- How should file downloads be tracked?

This shifted the mindset from <strong>application development</strong> to <strong>security operations</strong>.

</section>

<section id="requirements">

<h2>Logging Requirements Identified</h2>

Future logging includes:

- Successful and failed logins  
- File access and downloads  
- Application activity  
- Security events for SIEM ingestion  

This step laid the foundation for full SIEM integration later.

</section>

<section id="lessons2">

<h2> Lessons Learned</h2>

This integration journey reinforced several important principles:

<h3>1. Infrastructure problems often look like application bugs</h3>
Many failures were caused by networking and architecture, not code.

<h3>2. Authentication is easy — authorization is hard</h3>
Real access control depends on integrating with existing systems.

<h3>3. Containers introduce both portability and complexity</h3>
Docker solved reproducibility but required deeper networking knowledge.

<h3>4. Integration is the real challenge of security engineering</h3>
The hardest part was not building components, but making them work together.

</section>

<section id="conclusion">

<h2>Conclusion</h2>

What started as a simple proof-of-concept evolved into a realistic enterprise-style integration project.

The ADPanel now:

- Authenticates users via Active Directory  
- Accesses files via SMB using real permissions  
- Operates inside a containerized environment  
- Prepares events for SIEM monitoring  

This experience highlighted the complexity of real-world environments and laid the foundation for future detection engineering and monitoring scenarios.

</section>

</article>

