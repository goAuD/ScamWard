![Status: Verified](https://img.shields.io/badge/status-verified-brightgreen)
![Severity: Medium](https://img.shields.io/badge/severity-Medium-yellow)
![Analyst: Goaud](https://img.shields.io/badge/analyst-goaud-black)

# Incident Report – Notion / Stripe Verification SMS

**Status:** Resolved / case closed

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

## Date/Time

Timezone: CEST (UTC+02:00) unless otherwise stated. Times are approximate and
retained from contemporaneous notes.
Initial observation and report: 2025-08-22.
Stripe explanation received: 2025-08-23.
Notion case closed: 2025-08-26.

## Incident Type

- [x] Privacy / Data Protection Concern  

## Description

The user received unsolicited SMS verification codes from **Stripe** when logging in to **Notion** with the email `info@***.com`.  

Key facts:

- No phone number was ever provided to Notion.  
- No Stripe account had been created by the user.  
- Despite this, SMS messages with Stripe verification codes were received upon Notion login.  

This raised a privacy concern about an unexpected association between an email
address, a phone number, and a third-party verification flow. The available
evidence did not establish which system stored or supplied the association.

## Systems / Accounts Involved

- Notion account: `info@***.com`  
- Associated phone number: [redacted]  
- Email provider: Dynadot (custom domain)  

## Timeline

- **2025-08-22, approximately 18:00–18:30 CEST:** Unsolicited Stripe verification SMS observed and the issue reported to Stripe via chatbot support. Support requested a screenshot.
- **2025-08-22 18:30 CEST:** GDPR privacy request submitted to Notion Privacy team.
- **2025-08-22 19:25 CEST:** Auto-reply from Notion AI support.
- **2025-08-22 19:58 CEST:** Ticket merged into #4974768 by Notion support.
- **2025-08-23 10:20 CEST:** A change was observed in the Notion login interface: the verification field was no longer shown and the SMS messages ceased. The reason and timing of the product change were not confirmed by Notion.
- **2025-08-23 14:00 CEST:** Stripe support stated that a **Stripe Link account** had been created during a purchase at Elevenlabs.io (2025-08-14), and attributed the verification messages to that Link account.
- **2025-08-24:** Notion responded only with generic updates (“specialized team will investigate”), no official explanation yet.
- **2025-08-26:** Notion Privacy team replied formally: confirmed they had no phone number on file, attributed SMS behavior to Stripe Link, deleted the number from the ticket, and closed the case without root cause acknowledgement.
- **2025-08-22 → 2025-08-26:** Several Notion product updates were observed after the report. No evidence established that those updates were caused by this incident.

## Current Status

- **Stripe:** Resolved from the user's perspective. Stripe identified an existing Link account associated with an earlier transaction and provided options to log out, opt out of SMS, or delete the Link account.
- **Notion:** Case formally closed. Notion stated that it had no phone number on file and attributed the verification behavior to Stripe Link. It did not provide a separate technical root-cause analysis of the integration flow.

## Severity Assessment

**Impact:** Medium (unexpected cross-service identity and verification behavior).
**Likelihood:** Medium (reproducible account behavior with a vendor-provided explanation that was not independently verified).
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

**Notion:** Confirmed no phone number was stored in the Notion account and
attributed the behavior to Stripe. Notion removed the number from the support
ticket for privacy reasons and formally closed the case. A separate technical
root-cause explanation was not provided.

The vendor responses and observed interface change occurred after the report,
but the available evidence does not establish a causal relationship.

## Community Reports

A small number of public Reddit discussions describe similar unsolicited Stripe
Link verification messages. These reports are anecdotal, were not independently
verified for this investigation, and do not establish a shared root cause:

- [Discussion: unexpected Stripe verification messages](https://www.reddit.com/r/stripe/comments/b0tzot/got_a_random_text_of_a_code_from_stripe/)
- [Discussion: repeated verification messages](https://www.reddit.com/r/stripe/comments/1cx104c/verification_code_texts_to_my_ph_but_i_have_no/)

## Lessons Learned

- SaaS integrations can produce unexpected identity or verification flows across
  service boundaries. The responsible data flow should be verified before
  drawing conclusions about storage, consent, or fault.
- Privacy requests can provide a documented escalation path to vendor privacy
  and security teams.
- Maintaining a clear incident record strengthens the ability to track vendor responses and provides evidence if escalation to regulators (e.g. Austrian DSB) becomes necessary.
- Clear vendor communication helps users distinguish account behavior, product
  integration behavior, and confirmed security incidents.
- Product behavior can change while an investigation is open. Temporal
  correlation alone does not prove that a report caused the change.

## Final Notes & Lessons Learned (Legal-Neutral)

- Stripe identified a Link account associated with an Elevenlabs transaction and provided clear resolution steps.
- Notion stated that no phone number was stored on their side and attributed the SMS behavior to Stripe’s systems.
- Following the incident, the login flow changed and multiple product updates
  were observed. Their relationship to this report was not confirmed.
- Stripe supplied an account-level explanation. Notion supplied a privacy and
  account-data response but no separate integration-level root-cause analysis.
- Vendor statements and user-observed behavior should be recorded separately so
  later readers can distinguish evidence from interpretation.

> Status: The case is considered closed.
