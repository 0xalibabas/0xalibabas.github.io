+++
date = '2025-10-17T00:00:00-03:00'
draft = false
title = 'How zoho assist breaks the Windows security model'
tags = ['windows', 'privilege-escalation', 'remote-support', 'security-model', 'zoho']
+++

My original plan for this post was to kick things off with a series on Windows Internals and Windows apps bug bounty hunting. And I promise future posts won't be as "no actual exploit codes, just UI" as this one. However, security research happened. While analyzing a few applications, I came across an issue so weird and badly accepted that it deserved some attention.

This was the case with the Zoho Assist unattended agent. What appears to be a standard remote support tool is, in fact, an implementation with an architectural flaw that fundamentally breaks the Windows security model.

So, for this first post, I'll say why you should not neglect security boundaries and explain why the presence of the Zoho agent should be treated as a direct local privilege escalation.

![Zoho Assist UI](</images/Pasted image 20251017100314.png>)

### **Why this matters**

Windows separates user contexts for a reason. Standard user processes run at "medium integrity" and should not be able to force code to run at "system integrity." The unattended agent in current Zoho Assist deployments runs a service as `NT AUTHORITY\SYSTEM` and is designed to execute commands requested by a remote technician.

The problem is that this service accepts or forwards arbitrary commands originating from a lower integrity caller and executes those commands as `SYSTEM`. This means any local account that can interact with the agent can escalate privileges to full system compromise.

### **A quick recap: Integrity levels in Windows**

If you’ve never heard of integrity levels, here’s a high-level summary. They define how much trust a process has:

| **Level**     | **Typical Use**                             | **Privilege Summary**                                            |
| ------------- | ------------------------------------------- | ---------------------------------------------------------------- |
| **Untrusted** | Sandboxed content                           | Can barely do anything                                           |
| **Low**       | Browser sandboxes, UWP apps                 | Isolated; needs a broker to access most resources                |
| **Medium**    | Normal user applications                    | The default for anything you run                                 |
| **High**      | Elevated admin apps (after UAC)             | Can modify medium/low processes                                  |
| **System**    | Services, core OS components                | Has "full" local privileges; the keys to the kingdom             |
| **PPL**       | Hardened security services (Antivirus, DRM) | Even higher, access is controlled by its special signature level |

In short: low is a sandbox, medium is your everyday user space, high is "Run as Administrator," and system is the service layer. When a medium integrity process can directly influence a system integrity process to execute arbitrary code, that’s a textbook privilege escalation.

![Integrity diagram](</images/Pasted image 20251017101112.png>)

### How to test

- Install Zoho Assist Desktop.
- As a normal, non-admin user, connect to the machine from the Zoho mobile or second desktop client.
- Anything you send from the remote user will be ran as SYSTEM, so either spawn a cmd.exe or just add a new admin account on the Windows host. 

Spawning a SYSTEM cmd.exe:

<video controls src="/videos/zohoAssist_LPE.mp4"></video>

Or just choose "send files" option on the current desktop session:

<video controls src="/videos/20251017_095948.mp4"></video>

![Zoho file transfer](</images/Pasted image 20251017095509.png>)

### **Reporting to the Vendor**

Believing this to be a software design issue, I reported this through Zoho’s bug bounty channels and included reproduction details. The response I received was not what I expected:

> Hi,
> 
> Thank you for the report. After investigation, we have confirmed that the reported behavior is expected. Please refer to the following link for more information: https://www.zoho.com/assist/help/unattended-access.html.
>

![Zoho reply](</images/Pasted image 20251017093910.png>)

Their own documentation confirms this design. What.
The page they linked states that the agent is installed with "system-level privileges" to allow technicians to "perform administrative and troubleshooting tasks, even if the user logged into the remote device does not have administrative rights."

So, they are openly documenting that their agent provides a built-in privilege escalation path and calling it a feature. Let's be clear: a local privilege escalation vector is not a feature you want running on your endpoints.

This brings us to a critical principle in security: **documentation is not mitigation.** Calling a vulnerability "expected behavior" doesn't make it any less dangerous; it just confirms the flaw is into the fundamental design of the product.

### **Why this is an architectural issue**

Zoho's response confirms this isn't an accidental bug but a deliberate design choice, and that's precisely the problem.

- Integrity boundary violation: The Windows security model relies on these integrity levels to enforce security. A service that accepts arbitrary commands from lower integrity callers and executes them at SYSTEM breaks the security model.
- Token misuse: The design allows the service running as `SYSTEM` token to perform any action. A secure service should either heavily restrict what callers can request or perform actions by impersonating the caller's own medium integrity token.

### **Recommendations for defenders**

- Audit and Remove: Identify all hosts with the Zoho Assist unattended agent (ZohoURSService.exe). If it is not business critical for that machine, remove or disable the service.
- Application control: If the agent must remain, use application control solutions like AppLocker or Windows Defender Application Control to strictly limit which binaries the Zoho service is permitted to execute.

### **Closing notes**

Tools like Zoho Assist are nice, but when their agent is designed to bridge medium and system contexts without any meaningful security controls, the software itself becomes a privilege escalation tool. Zoho's stance that this is "expected" is concerning, as it shifts the burden of this architectural flaw onto their customers.
Don't get me wrong, performing security assessment on Windows apps is very cool, but unfortunately not all companies have the security maturity we'd like them to have....

![Closing image](</images/Pasted image 20251017110355.png>)

### Timeline

- Oct 9, 2025 - Reported to Zoho bug bounty
- Oct 14, 2025 - Zoho replied saying it's an "expected behavior"
- Oct 15, 2025 - When informed of my plan to publish, the vendor stated they would "neither explicitly allow nor prohibit" the disclosure
- Oct 17, 2025 - Blog published


