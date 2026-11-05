# Lesson 14 graphics

[Course graphics index](../README.md) · [Source chapter](../../lesson-14-integration-capstone.md)

Production: AI-generated illustrations using the built-in image_gen tool, continuing the course's navy, teal, and amber style. All figures are drafts for instructional review. The diagrams describe the bounded capstone, not a production backup system.

## Figure 14.1 — Keep the capstone inside one project

![Within linux-capstone, capstone.sh reads selected system facts into a report and archives only demo-data. The backups folder contains the archive and listing. The archive extracts into restore-check/demo-data, whose contents are compared with the original synthetic input.](01-project-boundary-data-flow.png)

- **Placement:** Section 3, after the explanation of report and archive contents.
- **Learning purpose:** Connect project paths to data flow and distinguish the archive input from other project artifacts.
- **Caption:** **Figure 14.1.** Keep reports, archives, listings, and restore tests inside `~/linux-capstone/`. The archive includes only synthetic `demo-data/`; selected system facts go to the report instead. Compare the restored data with the original, and review report details before sharing.
- **Alt text:** Within linux-capstone, capstone.sh reads selected system facts into a report and archives only demo-data. The backups folder contains the archive and listing. The archive extracts into restore-check/demo-data, whose contents are compared with the original synthetic input.
- **Prompt:** [01-generation-prompt.txt](01-generation-prompt.txt).
- **Review note:** TIMESTAMP stands for the script's time-based filename component, not literal text. The archive listing is a separate text file, not data implicitly included in the archive. The restore directory should be new and empty; mkdir -p alone does not establish emptiness. Paths are conceptual examples, not generated project artifacts. Timestamp names have seconds precision, so repeated or concurrent runs in one second can collide; inspect destinations rather than assuming uniqueness. No capstone script was run to create this illustration.

## Figure 14.2 — Verification answers different questions

![A syntax check establishes parsing, archive listing shows readable entries, extraction tests unpacking into a fresh directory, and diff recursively compares restored and source contents. No output plus diff exit status zero indicates no differences; these checks do not establish a separate failure domain or complete metadata equivalence.](02-verification-checks.png)

- **Placement:** Section 6, after the explanation of local restore-test limits.
- **Learning purpose:** Distinguish parsing, listing, restoration, content comparison, and independent-backup protection.
- **Caption:** **Figure 14.2.** Each check answers a different question. Syntax acceptance does not prove correct behavior; archive listing does not replace extraction; extraction does not replace comparison. Check both `diff -r` output and its status, and separately inspect report contents. A local restore test does not provide independent backup storage.
- **Alt text:** A syntax check establishes parsing, archive listing shows readable entries, extraction tests unpacking into a fresh directory, and diff recursively compares restored and source contents. No output plus diff exit status zero indicates no differences; these checks do not establish a separate failure domain or complete metadata equivalence.
- **Prompt:** [02-generation-prompt.txt](02-generation-prompt.txt).
- **Review note:** ARCHIVE, SOURCE, and RESTORE are placeholders for reviewed project paths. GNU diff returns 0 for no differences, 1 for differences, and 2 for trouble. Content comparison does not verify every permission, ownership, ACL, extended attribute, or recovery condition. The sample script checks archive operations but does not comprehensively check every report command; inspect the report and document failures. Use only a trusted archive and a fresh, empty restore directory.

## Figure 14.3 — Connect deliverables to evidence

![Six submission components map to the evidence they provide: script, redacted report and archive listing, restore comparison result, setup guide, security and troubleshooting notes, and reflection. A final review removes identifying details and confirms synthetic data.](03-deliverables-evidence.png)

- **Placement:** Student lab, after the deliverables and privacy guidance and before the knowledge check.
- **Learning purpose:** Link the submitted artifacts to observable acceptance criteria without treating a successful run as a complete project.
- **Caption:** **Figure 14.3.** Submit the script, redacted report and listing, restore-verification evidence, setup guide, security and troubleshooting notes, and reflection. Explain what each artifact demonstrates and what remains limited. Review all shared material for identifying information and non-synthetic data.
- **Alt text:** Six submission components map to the evidence they provide: script, redacted report and archive listing, restore comparison result, setup guide, security and troubleshooting notes, and reflection. A final review removes identifying details and confirms synthetic data.
- **Prompt:** [03-generation-prompt.txt](03-generation-prompt.txt).
- **Review note:** The chapter does not require an archive upload. Do not share an archive containing real data or raw host-specific reports. A missing or hypothetical troubleshooting issue must be labeled honestly; no repair or root cause should be invented. Evidence should describe the actual comparison result, not just that a command was attempted. Redact shared copies while preserving appropriate original project evidence locally.
