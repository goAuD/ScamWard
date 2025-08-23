# Incident Report – Notion / Stripe Verification SMS

*Status: Resolved*  

> **Disclaimer:**  
> This report is published for **research and educational purposes only**.  
> All personal or sensitive data has been anonymized or replaced with `***` or `[redacted]`.  
> No proprietary or confidential information is included.  
>
> The goal of this repository is to document suspected or confirmed security / privacy incidents as case studies, in line with standard SOC practices.  
> If you are the vendor mentioned in this report and would like to provide clarifications, please contact us.  

## Incident ID

SW-2025-0822-01  

## Reported by

Viktor [redacted]  

## Date/Time (UTC)

Initial report: 2025-08-22, 05:30 approx.  
Resolved: 2025-08-22, 13:00 approx.  

## Incident Type

- [x] Privacy / Data Protection Concern  

## Description

The user received unsolicited SMS verification codes from **Stripe** when logging in to **Notion** with the email `info@***.com`.  

Key facts:

- No phone number was ever provided to Notion.
- No Stripe account had been created by the user.  
- Despite this, SMS messages with Stripe verification codes were received upon Notion login.  

This suggested an unlawful or erroneous processing of personal data, where a phone number was linked to an email address without consent.  

## Systems / Accounts Involved

- Notion account: `info@***.com`  
- Associated phone number: [redacted]  
- Email provider: Dynadot (custom domain)  

## Timeline

- **2025-08-22 18:30 CET:** First unsolicited Stripe verification SMS received.  
- **2025-08-22 18:00 CET:** Issue reported to Stripe via chatbot support (screenshot requested).  
- **2025-08-22 18:30 CET:** GDPR privacy request submitted to Notion Privacy team.  
- **2025-08-22 19:25 CET:** Auto-reply from Notion AI support.  
- **2025-08-22 19:58 CET:** Ticket merged into #4974768 by Notion support.  
- **2025-08-23 10:20 CET:** Notion login interface updated – verification field removed, SMS messages ceased.  

## Current Status

**Resolved.** After a Notion update on the same day, the verification field disappeared and no further SMS messages were received.  

## Severity Assessment

**Impact:** Medium (possible unlawful processing of personal data).  
**Likelihood:** Medium (misconfiguration or integration error).  
**Overall Severity:** Medium.  

## Actions Taken

- Issue reported to Stripe with supporting evidence (screenshot).  
- GDPR privacy request filed with Notion.  
- Notion account password changed.  
- 2FA enabled on Notion account.  

## Resolution

The incident appears to have been mitigated through a Notion platform update on 2025-08-22. The unsolicited SMS behavior stopped immediately after the change. While no official root cause was communicated at the time of writing, the sequence of events suggests a misconfigured Stripe integration in the Notion login flow.  

It is likely that the reporting of this incident directly contributed to the vendor’s rapid deployment of a fix.  

## Lessons Learned

- SaaS platform integrations (e.g. Notion ↔ Stripe) may propagate personal data without user consent if misconfigured.  
- GDPR privacy requests are effective in escalating such issues to vendor privacy/security teams.  
- Maintaining a clear incident record strengthens the ability to track vendor responses and provides evidence if escalation to regulators (e.g. Austrian DSB) becomes necessary.  
