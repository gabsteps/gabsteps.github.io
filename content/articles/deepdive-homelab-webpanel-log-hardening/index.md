---
title: "Homelab SIEM Log Hardening"
date: 2026-05-10
summary: "Deep Dive, log hardening on my homelab"
layout: "single"
tags: ["Logs", "Homelab", "Wazuh", "Deep Dive", "GPO"]
---
<article>

<header>
<h1>Deep Dive — Log Hardening in the Homelab (Wazuh + Sysmon + GPO)</h1>
<p>After building the WebPanel and deploying the SIEM in my homelab, the next natural step was improving <strong>log quality</strong>.</p>
<p>A SIEM without rich telemetry gives limited visibility.</p>
<p>This deep dive shows how I enriched log telemetry</p>
</header>

<section id="goal">
<h2>Goal of Log Hardening</h2>

<p>By default, Windows generates very limited security telemetry.</p>

<p>Examples of what is <strong>not</strong> enabled by default:</p>

<ul>
<li>Detailed process creation</li>
<li>Command line logging</li>
<li>User creation visibility</li>
<li>Persistence activity</li>
<li>Lateral movement indicators</li>
<li>Service creation</li>
<li>Full PowerShell logging</li>
</ul>

<p>Without hardening, the SIEM is basically blind.</p>

<p>Goals of this phase:</p>

<ul>
<li>Centralize logs into Wazuh</li>
<li>Enrich Windows telemetry</li>
<li>Standardize mass deployment via GPO</li>
<li>Prepare the environment for detection testing</li>
</ul>
</section>

<section id="architecture">
<h2>Environment Architecture</h2>

<p>Domain lab components:</p>

<ul>
<li>Domain Controller</li>
<li>Windows client machine</li>
<li>Wazuh Server (SIEM)</li>
<li>SYSVOL shared folder for automated deployment</li>
</ul>

<p>Final pipeline:</p>

<pre><code>Windows Hosts → Sysmon + Windows Logs → Wazuh Agent → Wazuh Server → Dashboard</code></pre>
</section>

<section id="step1">
<h2>Step 1 — Initial Problem</h2>

<p>After installing the Wazuh agent, the dashboard showed almost no meaningful events.</p>
<p>This is expected — Windows logs are minimal by default.</p>
<p>Time to start the <strong>Log Hardening</strong> process.</p>
</section>

<section id="step2">
<h2>Step 2 — Why Sysmon Matters</h2>

<p>Windows Event Log is focused on administrative auditing.</p>
<p>Sysmon is focused on <strong>security telemetry</strong>.</p>

<p>It adds critical visibility such as:</p>

<ul>
<li><strong>Process Creation</strong> - Detects malware execution</li>
<li><strong>Network Connections</strong> - Detects beaconing and command-and-control traffic</li>
<li><strong>File Creation</strong> - Detects droppers and payload staging</li>
<li><strong>Registry Changes</strong> - Detects persistence mechanisms</li>
<li><strong>Service Creation</strong> - Detects privilege escalation and lateral movement</li>
</ul>

<p>Without Sysmon, a SIEM loses ~70% of endpoint visibility.</p>

</section>

<section id="step3">
<h2>Step 3 — Mass Deployment via GPO</h2>

<p>Manual installation does not scale.</p>
<p>Realistic scenario: <strong>100+ endpoints</strong></p>
<p>Solution: automated deployment via GPO.</p>

<h3>Domain share structure</h3>

<pre><code>\\domain\SYSVOL\scripts\
 ├── sysmon.exe
 ├── sysmonconfig.xml
 └── install_sysmon.ps1
</code></pre>

<p>GPO path:</p>

<pre><code>Computer Configuration
 → Policies
   → Windows Settings
     → Scripts (Startup)</code></pre>

<p>Result:<br>
Every domain machine installs Sysmon automatically at boot.</p>

<p>This step makes the lab much closer to real-world environments.</p>
</section>

<section id="step4">
<h2>Step 4 — Windows Audit Policy Hardening</h2>

<p>Even with Sysmon, the Windows Security Log remains essential.</p>
<p>Enabled via GPO:</p>

<h3>Advanced Audit Policy Configuration</h3>

<p>Key categories enabled:</p>

<h4>Account Logon / Logoff</h4>
<ul>
<li>Logon / Logoff</li>
<li>Credential validation</li>
<li>Remote logons</li>
<li>RDP activity</li>
</ul>

<h4>Account Management</h4>
<ul>
<li>User creation</li>
<li>Password changes</li>
<li>Group membership changes</li>
</ul>

<h4>Object Access</h4>
<ul>
<li>File access</li>
<li>Registry access</li>
</ul>

<h4>Policy Change</h4>
<ul>
<li>Security policy modifications</li>
</ul>

<h4>Privilege Use</h4>
<ul>
<li>Administrative privilege usage</li>
</ul>

<p>This adds visibility into:</p>

<ul>
<li>Suspicious user creation</li>
<li>Brute force attempts</li>
<li>Privilege escalation</li>
<li>Credential abuse</li>
</ul>
</section>

<section id="step5">
<h2>Step 5 — Wazuh Receiving Telemetry</h2>

<p>After rebooting the endpoints, new events started appearing in the dashboard.</p>

<p>Events:</p>
<ul>
<li>Process creation</li>
<li>Network connections</li>
<li>PowerShell activity</li>
<li>Logons</li>
</ul>

</section>

<section id="result">
<h2>Final Result</h2>

<p>Before hardening:</p>
<ul>
<li>Few events</li>
<li>Limited visibility</li>
<li>Dashboard Events mostly empty</li>
</ul>

<p>After hardening:</p>
<ul>
<li>Rich telemetry</li>
<li>Real-time events</li>
<li>Detection-ready environment</li>
</ul>

<p>The SIEM moved from <strong>poor logging</strong> to <strong>rich logging</strong>.</p>

</section>

</article>
