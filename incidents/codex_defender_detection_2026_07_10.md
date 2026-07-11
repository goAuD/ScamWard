![Status: Contained](https://img.shields.io/badge/status-contained-blue)
![Severity: Low](https://img.shields.io/badge/severity-low-blue)
![Classification: Suspected False Positive](https://img.shields.io/badge/classification-suspected%20false%20positive-yellow)
![Analyst: Goaud](https://img.shields.io/badge/analyst-goaud-black)

# Incident Report – Microsoft Defender Detection in OpenAI Codex Runtime

**Status:** Contained / vendor validation pending  
**Incident ID:** SW-2026-0710-01  
**Date:** 2026-07-10  
**Timezone:** CEST (UTC+02:00)  
**Category:** Endpoint security alert / suspected false positive  
**Final incident severity:** Low  

> **Disclaimer:**  
> This report documents a user-observed endpoint security alert for research and
> educational purposes. Sensitive prompt content, thread identifiers, account
> data, and local user-specific paths have been redacted. The detection has not
> received a final determination from Microsoft or OpenAI. Statements below
> distinguish observed evidence from analyst inference.

## Executive Summary

Microsoft Defender repeatedly identified an OpenAI Codex runtime helper as
`Trojan:Win32/PowhidSubExec.B` when a Codex agent turn completed in Visual
Studio Code.

The Defender event referred to a `CmdLine:` resource rather than a quarantined
file object. The command line contained serialized agent-turn metadata and
conversation history. That history included text from an earlier PowerShell
preview helper that used hidden subprocess startup. Subsequent `turn-ended`
calls carried the same historical text again, producing repeated detections.

No active threat, successful malware execution, repository-delivered native
binary, credential exposure, or confirmed compromise was found. The available
evidence is consistent with a command-line heuristic false positive. However,
the directly flagged helper is not individually Authenticode-signed and has no
public reference hash, so the false-positive classification remains pending
vendor validation.

## Systems and Components Involved

- Microsoft Windows 11 Pro, build `26200`
- Microsoft Defender Antivirus
- Visual Studio Code
- Official OpenAI Codex extension, publisher `openai`
- Extension version `26.707.31428`
- Bundled package `@oai/sky` version `0.4.19`
- Helper executable: `%LOCALAPPDATA%\OpenAI\Codex\runtimes\cua_node\<runtime>\bin\node_modules\@oai\sky\bin\windows\codex-computer-use.exe`
- Project under review at detection time: a SvelteKit/Convex StudentApp PWA
  pull request

## Detection Evidence

| Field | Observed value |
| --- | --- |
| Defender detection | `Trojan:Win32/PowhidSubExec.B` |
| Threat ID | `2147941383` |
| Defender severity | Severe (`SeverityID: 5`) |
| Defender category | Trojan (`CategoryID: 8`) |
| Detected resource type | `CmdLine` |
| `IsActive` | `false` |
| `DidThreatExecute` | `false` |
| Remediation result | Successful for each recorded event |
| Defender signatures | `1.455.72.0` |
| Defender engine | `1.1.26060.3008` |
| Helper size | `1,691,648` bytes |
| Helper SHA-256 | Recorded below |
| Helper Authenticode status | Not signed |

```text
F2B2F56FCD1699B0FA32DEC3214A56A1D36B937A2ECF58CC822AB4A904551E03
```

Four matching detections were recorded:

- 2026-07-10 20:09:23 CEST
- 2026-07-10 20:09:56 CEST
- 2026-07-10 20:21:38 CEST
- 2026-07-10 20:23:40 CEST

No later matching detection was present during the final evidence collection.

## Timeline

- **2026-07-01:** The Codex runtime helper was installed as part of the local
  Codex runtime, before the project pull-request work began.
- **2026-07-08:** The first feature commit in the reviewed PWA branch was
  created.
- **2026-07-10, before 20:09 CEST:** A temporary PowerShell-based preview helper
  used hidden process startup to launch a local Vite preview and clean up the
  test process. The helper was stopped and the remaining preview process was
  terminated by explicit process ID.
- **2026-07-10, 20:09–20:23 CEST:** Defender generated four
  `Trojan:Win32/PowhidSubExec.B` detections on Codex `turn-ended` command lines.
- **2026-07-10, after 20:23 CEST:** Defender state was checked. The threat was
  inactive, Defender reported that it had not executed, and all recorded
  remediation actions were successful.
- **2026-07-10:** The local Codex thread and user-reported `.codex` working data
  were removed as a containment and privacy step.

## Investigation Performed

### 1. Defender event review

The Microsoft Defender Operational event log and Defender PowerShell cmdlets
were inspected. Every matching detection identified the resource as a command
line. The event data did not identify a known executing malware process.

### 2. File provenance and signature review

The flagged helper existed under the installed OpenAI Codex runtime and matched
the path expected by the bundled `@oai/sky` package documentation. That local
documentation states that the Windows helper is produced by an OpenAI build
workflow and packaged at `bin/windows/codex-computer-use.exe`.

The helper itself was not Authenticode-signed. In the same Codex installation:

- `codex.exe` had a valid `OpenAI OpCo, LLC` signature.
- `codex-command-runner.exe` had a valid `OpenAI OpCo, LLC` signature.
- `codex-windows-sandbox-setup.exe` had a valid `OpenAI OpCo, LLC` signature.
- The bundled Node.js executable had a valid OpenJS Foundation signature.

The private `@oai/sky` package was not available from the public npm registry,
so an independent comparison against a public package hash was not possible.

### 3. Command-line data-flow review

The detected `turn-ended` command carried serialized JSON containing agent-turn
metadata and historical input messages. A previous message contained a hidden
PowerShell `Start-Process` pattern. The historical text was present as data in
the JSON argument; there was no evidence that the text was reinterpreted as a
new PowerShell command by the `turn-ended` invocation.

This explains why the same heuristic could be triggered after each completed
agent turn even though no new preview helper was launched.

### 4. Project repository correlation

The pull request active at the time was inspected for a delivery or persistence
path:

- The flagged Codex helper predated the pull-request work by seven days.
- No native `.exe`, `.dll`, `.msi`, or similar executable was tracked by the
  project repository.
- No `Start-Process`, `previewProcess`, encoded PowerShell, download-and-execute,
  or equivalent subprocess pattern was found in the pull-request diff or Git
  history.
- The pull request did not add an npm dependency or modify a lockfile.
- The only changed PowerShell file expanded Semgrep scan targets and did not
  launch a hidden process.

No evidence linked the Defender alert to code delivered by the reviewed pull
request.

## Root Cause Assessment

### Observed

- Defender classified the resource as `CmdLine`.
- The command line contained historical text matching a suspicious hidden
  PowerShell subprocess pattern.
- Repeated alerts aligned with Codex agent-turn completion.
- Defender reported no active threat and no threat execution.
- The flagged executable remained in the expected Codex runtime location.

### Inferred

The most likely cause is a Defender command-line or behavioral heuristic that
matched serialized conversation content passed to the Codex `turn-ended`
helper. Repeated inclusion of the same historical PowerShell text caused the
alert to recur.

### Not confirmed

- Microsoft has not issued a sample-analysis verdict.
- OpenAI has not supplied a public reference hash or signed replacement for the
  helper.
- Therefore, this report does not claim that the binary is definitively clean.

## Impact Assessment

### Confirmed impact

- Repeated high-severity endpoint alerts interrupted development work.
- Investigation and containment time was required.
- Conversation content was copied into local process-command-line and Defender
  event data, creating a local privacy and log-retention concern.

### No confirmed impact

- No credential theft was observed.
- No unauthorized persistence was found.
- No malicious network activity was attributed to the helper.
- No project source file or dependency was identified as a malware delivery
  mechanism.
- No data loss or account compromise was identified.

## Actions Taken

- Stopped using the hidden preview-helper pattern.
- Terminated the remaining Vite preview process by explicit process ID.
- Inspected Defender threat state and Operational event records.
- Calculated the helper SHA-256 hash.
- Checked Authenticode signatures for the Codex runtime executables.
- Reviewed the active project diff and Git history for executable delivery and
  suspicious subprocess patterns.
- Removed the affected Codex thread and user-reported `.codex` working data.
- Did not add a broad Microsoft Defender exclusion.

## Recommended Follow-up

1. Submit the helper file or SHA-256 hash to Microsoft as `Clean (false
   positive)` and retain the submission ID. **Done:** submitted via the
   Microsoft Security Intelligence Software Developer submission portal
   on 2026-07-11 as "Incorrectly detected as malware/malicious",
   detection name `Trojan:Win32/PowhidSubExec.B`, definition version
   `1.455.72.0`. Submission ID `64b28adb-3938-41dd-abf2-e518349505ca`,
   status: Submitted. Vendor determination pending.
2. Report the issue to OpenAI with the extension version, package version,
   threat name, hash, Defender engine/signature versions, and a redacted event
   sample. **Done:** filed as
   [openai/codex#32343](https://github.com/openai/codex/issues/32343)
   on 2026-07-11. Identified as a duplicate of the same underlying issue
   reported four days earlier in
   [openai/codex#31419](https://github.com/openai/codex/issues/31419)
   (identical helper SHA-256, different Defender detection name:
   `Trojan:Win32/ClickFix.DE!MTB`) — closed #32343 in favor of #31419
   after cross-linking both reports to consolidate the evidence.
3. Do not submit the complete detected command line because it may contain
   historical prompts or other private context.
4. Update or reinstall the Codex extension only from the official OpenAI
   publisher and verify whether the runtime helper changes.
5. Test in a new, empty Codex thread without copying the historical PowerShell
   command. If the alert returns, stop using the affected helper and run a
   Microsoft Defender Offline scan.
6. Avoid broad allow-listing of the Codex runtime. Create a narrow exception
   only after a clean vendor determination, if an exception remains necessary.
7. OpenAI should Authenticode-sign the Windows helper and publish verifiable
   release hashes or signed package metadata.

## Lessons Learned

- Security tools can match suspicious text carried as process arguments even
  when that text is data rather than executable code.
- Repeated detections do not necessarily mean repeated reinfection; correlation
  with lifecycle events and resource type is essential.
- High-severity antivirus labels must be investigated, not automatically
  dismissed as false positives.
- Full command lines can contain sensitive application context and should be
  redacted before sharing with vendors or publishing incident reports.
- Independently signed helper binaries and public release hashes materially
  improve incident triage.

## References

- [Microsoft: Submit files for analysis](https://learn.microsoft.com/en-us/unified-secops/submission-guide)
- [Microsoft Defender for Endpoint: Submit files or file hashes](https://learn.microsoft.com/en-us/defender-endpoint/admin-submissions-mde)
- [OpenAI Codex use cases](https://developers.openai.com/codex/use-cases)
- [OpenAI Codex issue tracker report: openai/codex#32343](https://github.com/openai/codex/issues/32343)
  (closed as duplicate of
  [openai/codex#31419](https://github.com/openai/codex/issues/31419))

## Final Status

The incident is contained. No active threat or project-repository compromise
was identified. The leading assessment is a command-line heuristic false
positive, with final classification pending Microsoft or OpenAI confirmation.
