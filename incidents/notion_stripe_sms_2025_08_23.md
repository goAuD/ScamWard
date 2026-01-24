![Status: Verified](https://img.shields.io/badge/status-verified-brightgreen)
![Severity: Medium](https://img.shields.io/badge/severity-Medium-yellow)
![Analyst: Goaud](https://img.shields.io/badge/analyst-goaud-black)

# Incident Report – Notion / Stripe Verification SMS

*Status: Resolved (Stripe) / Pending (Notion)*  

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

goaud [redacted]  

## Date/Time (UTC)

Initial report: 2025-08-22, 05:30 approx.  
Resolved (Stripe): 2025-08-22, 13:00 approx.  
Notion status: Pending (as of 2025-08-24)
Closed (Notion): 2025-08-26

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
- **2025-08-23 14:00 CET:** Stripe confirmed root cause – a **Stripe Link account** was automatically created during a purchase at Elevenlabs.io (2025-08-14). This triggered SMS verifications via Notion’s integration.  
- **2025-08-24:** Notion responded only with generic updates (“specialized team will investigate”), no official explanation yet.
- **2025-08-26:** Notion Privacy team replied formally: confirmed they had no phone number on file, attributed SMS behavior to Stripe Link, deleted the number from the ticket, and closed the case without root cause acknowledgement.
- **2025-08-22 → 2025-08-26:** Three smaller Notion updates observed after the incident, indicating silent remediation, though not officially acknowledged.

## Current Status

- **Stripe:** Resolved. Root cause identified and explained transparently. Options provided to log out, opt-out of SMS, or delete Link account.  
- **Notion:** Notion: Case formally closed. No phone number on file, responsibility attributed fully to Stripe. No official acknowledgement of integration’s role. Silent fix indicators observed.

## Severity Assessment

**Impact:** Medium (possible unlawful processing of personal data).  
**Likelihood:** Medium (misconfiguration or integration error).  
**Overall Severity:** Medium.  

## Actions Taken

- Issue reported to Stripe with supporting evidence (screenshot).  
- GDPR privacy request filed with Notion.  
- Notion account password changed.  
- 2FA enabled on Notion account.  
- SMS screenshot shared with Stripe for investigation.
- Final responses documented from both vendors.

## Resolution

**Stripe:** Confirmed the existence of a Stripe Link account tied to the email address due to an Elevenlabs.io transaction. Explained why SMS codes were sent, and provided mitigation/deletion options. Handled with transparency and diligence.

**Notion:** Confirmed no phone number on file and attributed the behavior to Stripe. Deleted number from ticket for privacy reasons. Login update removed verification field and SMS behavior ceased, but no root cause acknowledged. Case formally closed.

It is likely that the reporting of this incident contributed directly to the vendors’ rapid actions (Stripe clarification, Notion silent remediation).

## Community Reports

Several users on Reddit and other forums reported identical experiences of unsolicited Stripe verification codes linked to Notion. This indicates the incident was not isolated but systemic. Examples:

> "when i go to the settings in Notion, i am getting an ‘xxxxxx is your Link verification code’ SMS verification too… it stopped once i verified it" — schylermanning ([reddit.com](https://www.reddit.com/r/stripe/comments/b0tzot/got_a_random_text_of_a_code_from_stripe/?utm_source=chatgpt.com))  
> "I received 4 text verification codes and I have no accounts with stripe." — Top_Drive_487 ([reddit.com](https://www.reddit.com/r/stripe/comments/b0tzot/got_a_random_text_of_a_code_from_stripe/?utm_source=chatgpt.com))  
> "Claude.ai – sent me a code before I even knew I needed one… should definitely try to add a footer…" — Skeptical_Optimist88 ([reddit.com](https://www.reddit.com/r/stripe/comments/b0tzot/got_a_random_text_of_a_code_from_stripe/?utm_source=chatgpt.com))  
> "These cunts keep spamming me with this shit…" — Leprichaun17 ([reddit.com](https://www.reddit.com/r/stripe/comments/1cx104c/verification_code_texts_to_my_ph_but_i_have_no/?utm_source=chatgpt.com))  

## Lessons Learned

- SaaS platform integrations (e.g. Notion ↔ Stripe) may propagate personal data without user consent if misconfigured.
- GDPR privacy requests are effective in escalating such issues to vendor privacy/security teams.
- Maintaining a clear incident record strengthens the ability to track vendor responses and provides evidence if escalation to regulators (e.g. Austrian DSB) becomes necessary.
- Transparency in vendor communication (as shown by Stripe) is crucial. Lack of communication or defensive positioning (as seen with Notion) may undermines user trust.
- Silent fixes may occur without vendor acknowledgement, making user-side monitoring and documentation important.

## Final Notes & Lessons Learned (Legal-Neutral)

- Stripe confirmed the root cause (Stripe Link auto-account via Elevenlabs) and provided clear resolution steps.
- Notion stated that no phone number was stored on their side and attributed the SMS behavior to Stripe’s systems.
- Following the incident, the login flow changed and multiple product updates were observed, though no official acknowledgement was made.
- This case highlights the difference in vendor response styles:
  - Stripe: transparent, solution-oriented, trust-first.
  - Notion: formal, compliance-focused, risk-minimization.
- Key takeaway: vendor communications may not always align with observed user experience, which makes incident documentation and timeline tracking essential.

> Status: The case is considered closed.
