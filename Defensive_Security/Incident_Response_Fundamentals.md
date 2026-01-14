---
Main Topic: Defensive_Security
Sub Topic: Incident_Response_Fundamentals
Links:
  - https://tryhackme.com/room/incidentresponsefundamentals
  - https://www.ibm.com/think/topics/incident-response
Date: 14-01-2026
Related Notes: 
tags:
  - Cyber_Security
  - THM
---

# Incident_Response_Fundamentals

> [!INFO] Incident Response
> Incident response (sometimes called cybersecurity incident response) refers to an organization’s processes and technologies for detecting and responding to cyberthreats, security breaches or cyberattacks. A formal incident response plan enables cybersecurity teams to limit or prevent damage.

The goal of incident response is to prevent cyberattacks before they happen and minimize the cost and business disruption resulting from any cyberattacks that occur. Incident response is the technical portion of incident management, which also includes executive, HR and legal management of a serious incident.

Ideally, an organization defines incident response processes and technologies in a formal incident response plan (IRP) that specifies how different types of cyberattacks should be identified, contained and resolved.

An effective incident response plan can help cyber incident response teams detect and contain cyberthreats, restore affected systems and reduce lost revenue, regulatory fines and other costs.

So, when a security solution finds an event or group of events associated with a possible harmful activity, it triggers an alert. The security team then analyzes these alerts. Some of these alerts may be **False Positives**, while some would be **True Positives**. The alerts that point to something dangerous but are not harmful are referred to as false positives. In contrast, the alerts that point to something harmful and are actually dangerous are called true positives. You can get a better understanding of this from the example below:

- **False positive:** A security solution raised an alert on a high amount of data being transferred from one system to an external IP address. Upon analyzing this alert, the security team found that the subject system was undergoing a backup process to a cloud storage service, which caused this. This is known as a false positive.

- **True Positive:** A security solution raised an alert on a phishing attempt on one of the organization’s users. Upon analyzing this alert, the security team found that the email was a phishing email sent to this user to compromise the system. This is known as a true positive.

These true positive alerts are sometimes referred to as **Incidents**. Assuming the alert is now categorized as an incident, the next phase is to give a severity level to the incident. Imagine you are part of the security team and get multiple incidents simultaneously. Which incident would you choose first to respond to? This is where the idea of incident severity helps. Incidents can be categorized as low, medium, high, or critical based on the impact they can create. Critical Severity incidents are always the higher priority, followed by high severity, and so on.

---

## What are security incidents?

A security incident, or security event, is any digital or physical breach that threatens the confidentiality, integrity or availability of an organization’s information systems or sensitive data. Security incidents can range from intentional cyberattacks by hackers or unauthorized users, to unintentional violations of policy by legitimate authorized users.

Some of the most common security incidents include:

### Ransomware

is a type of malicious software, or , that locks up a victim's data or computing device and threatens to keep it locked, or worse, unless the victim pays a ransom. The latest *X-Force Threat Intelligence Index* from IBM reports that 20% of network attacks used ransomware and that extortion-based attacks are a driving force in cybercrime, only surpassed by data theft and leaks.  

attacks are digital or voice messages that try to manipulate recipients to share sensitive information, download malicious software, transfer money or assets to the wrong people or take some other damaging action.  

Attackers craft phishing messages to look or sound as if they come from a trusted or credible organization or individual, sometimes even an individual the recipient knows personally.

Phishing and stolen or compromised credentials are the two most prevalent attack vectors, according to the IBM Cost of a Data Breach report. Phishing is also the most common form of , a class of attack that hacks human nature rather than digital security vulnerabilities to gain unauthorized access to sensitive personal or enterprise data or assets.

### DDoS attacks

In a attack, hackers gain control of large numbers of computers and use them to overwhelm a target organization’s network or servers with bogus traffic, making those resources unavailable to legitimate users.  

### Supply chain attacks

Supply chain attacks are cyberattacks that infiltrate a target organization by attacking its vendors. For example, this could include stealing sensitive data from a supplier’s systems or using a vendor’s services to distribute malware.  

### Insider threats

There are two types of . *Malicious insiders* are employees, partners or other authorized users who intentionally compromise an organization’s information security. *Negligent insiders* are authorized users who unintentionally compromise security by failing to follow security best practices by, say, using weak passwords or storing sensitive data in insecure places.  

### Privilege escalation attacks

These involve an attacker who first gains limited privileges in a system and uses those to move laterally, receiving higher privileges and gaining access to more sensitive data along the way.  

Stolen credentials can help the attacker with either the initial entry or boosting their privileges. According to the *X-Force Threat Intelligence Index*, the abuse of valid accounts is the most common way that attackers breach systems today.

### Man-in-the-middle (MITM) attacks

In an MITM attack, the intercepts a communication, often an email containing sensitive information such as usernames or passwords, and either steals or alters that communication. The attacker either uses the stolen information directly or injects malware to be forwarded to the intended recipient. 

---
## Incident Response Plans

Sometimes, handling a variety of incidents in an environment can be difficult. Due to the distinct nature of incidents in organizations, there should be a structured process for incident response. Incident Response Frameworks help us in this regard. These are the generic approaches to follow in any incident for effective response. We will discuss the two widely used incident response frameworks: SANS[^1] and NIST.

SANS and NIST are popular organizations contributing to cyber security. SANS has offered various courses and certifications in cyber security, and NIST played its role in developing standards and guidelines for cyber security. Both SANS and NIST have quite similar incident response frameworks.

The SANS incident Response framework has 6 phases, which can be called 'PICERL' to remember them easily.

| Phase | Explanation | Example |
| --- | --- | --- |
| Preparation | This is the first phase. The preparation phase includes building the necessary resources to handle an incident. These resources include developing incident response teams, having a proper incident response plan in place, and deploying necessary security solutions to combat the incidents. | Conducting awareness training for employees on phishing emails. Phishing emails are fraudulent emails sent by malicious attackers that can trick you into performing actions that can lead you to an incident. |
| Identification | The identification phase refers to looking for any abnormal behavior that may indicate an incident. This involves using various security solutions and techniques to monitor abnormal events. | The security team notices a huge amount of data being sent out from one of the hosts. Upon analysis, it was found to be compromised after a malicious file was downloaded from a phishing email attachment. |
| Containment | Once an incident has been identified, the next step should be to contain it. This means minimizing the impact of the attack. This is usually done by isolating the victim machine, disabling the compromised user accounts, etc. | The Security team isolates the host from the network to minimize the impact and not allow the attacker to jump to other systems, leveraging the compromised host. |
| Eradication | This phase, as its name suggests, involves removing the threat from the attacked environment. The threat may be of any kind. The eradication phase will ensure the subject environment is clean, and now we can move to the recovery phase. | A deep malware scan was executed on the system to remove the malicious software from the host. |
| Recovery | The recovery phase is very important in this chain. It involves recovering the affected systems from backup or rebuilding them. The recovered systems are then tested and are ready to use. | The compromised host was re-configured, and the exfiltrated data was restored from the backup. |
| Lessons Learned | This is also an important part of the incident response lifecycle. Gaps in the detection and analysis of the incident are identified and documented, helping to improve the overall process in future incidents. | Conducting a post-incident review meeting to analyze the incident's root cause and improve the security to prevent future attacks. |

The Incident Response Framework of NIST is similar to the SANS framework we studied above. The number of phases in this framework is reduced to 4.

Following is the comparison of both:

![[Pasted image 20260114162329.png]]  

Organizations may derive their incident response processes by following these frameworks. Every process has a formal document listing all the relevant organizational procedures. The formal incident response document is called the **Incident Response Plan**. This structured document underlines the approach during any incident. It is formally approved by senior management and consists of the procedures to be followed before, during, and after an incident has been completed.

The key components of this plan include (and are not limited to):

- Roles and Responsibilities
- Incident Response methodology
- Communication plan with stakeholders, including law enforcement
- Escalation path to be followed


[^1]: Per 2021, SANS is the world's largest cybersecurity research and training organization. SANS is an acronym for **SysAdmin, Audit, Network, and Security**.