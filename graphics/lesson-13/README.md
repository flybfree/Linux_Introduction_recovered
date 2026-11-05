# Lesson 13 graphics

[Course graphics index](../README.md) · [Source chapter](../../lesson-13-troubleshooting-documentation.md)

Production: AI-generated illustrations using the built-in image_gen tool, continuing the course's navy, teal, and amber style. All figures are drafts for instructional review. The student lab remains read-only; examples use conceptual or synthetic evidence.

## Figure 13.1 — Investigate, test, and revise

![Define a symptom, scope impact, record changes, and collect evidence before forming a hypothesis and prediction. Run one authorized low-risk test, compare the result, and revise when unsupported or inconclusive. Supported explanations lead to an authorized remedy or escalation, original-task verification, and documentation.](01-investigation-loop.png)

- **Placement:** Section 2, after the explanation of the iterative investigation method.
- **Learning purpose:** Show troubleshooting as a hypothesis-testing loop rather than a sequence of guessed repairs.
- **Caption:** **Figure 13.1.** Define and scope the symptom, collect evidence, and test a prediction one variable at a time. Results may support, weaken, or leave a hypothesis unresolved. Revise as needed, resolve or escalate within authorization, verify the original task, and document uncertainty as well as outcomes.
- **Alt text:** Define a symptom, scope impact, record changes, and collect evidence before forming a hypothesis and prediction. Run one authorized low-risk test, compare the result, and revise when unsupported or inconclusive. Supported explanations lead to an authorized remedy or escalation, original-task verification, and documentation.
- **Prompt:** [01-generation-prompt.txt](01-generation-prompt.txt).
- **Review note:** Support for a prediction is not proof of a unique root cause. Escalation can occur at any point when impact, missing authority, suspected compromise, data loss, or uncertainty warrants it. If escalation occurs before repair, document verification as pending; the next owner performs any authorized remedy and original-task test. Disproved hypotheses remain useful evidence.

## Figure 13.2 — From an observation to a testable hypothesis

![A synthetic service log reports Permission denied and the unit reports failed. These observations support an access-related inference, not a confirmed claim that group ownership is wrong. A testable hypothesis about the service identity's access leads to inspecting the configured path, identity, and path permissions.](02-observation-inference-hypothesis.png)

- **Placement:** Section 3, after the distinction between direct evidence and interpretation.
- **Learning purpose:** Apply observation, inference, and hypothesis to the chapter's permission-denied scenario without inventing a confirmed cause.
- **Caption:** **Figure 13.2.** A permission-denied message is an observation. An access-related explanation is an inference until checked. Make the hypothesis testable by identifying what should be inspected next: the intended path, service identity, and applicable access controls. Do not broaden permissions based on a guess.
- **Alt text:** A synthetic service log reports Permission denied and the unit reports failed. These observations support an access-related inference, not a confirmed claim that group ownership is wrong. A testable hypothesis about the service identity's access leads to inspecting the configured path, identity, and path permissions.
- **Prompt:** [02-generation-prompt.txt](02-generation-prompt.txt).
- **Review note:** The shortened data.db excerpt is adapted teaching evidence, not output collected from a live system. A file's bits are only part of access: parent traversal, ACLs, mandatory controls, namespace/mount restrictions, and configuration can matter. A useful prediction is that the configured identity will lack the required access at the actual path if this explanation is correct; a check that contradicts that prediction should prompt revision.

## Figure 13.3 — Turn evidence into a useful handoff

![Timestamped symptoms, brief evidence excerpts, and test results pass through redaction and context review into a troubleshooting record. The record includes impact, environment, timeline, expected and actual behavior, evidence, hypothesis, tests, actions, verification, status, and next owner. Resolved, mitigated, and escalated are alternative outcomes.](03-evidence-to-handoff.png)

- **Placement:** Section 10, after the documentation guidance and before guided demonstrations.
- **Learning purpose:** Connect working notes to a concise handoff another person can use without implying an unperformed repair or confirmed cause.
- **Caption:** **Figure 13.3.** Turn relevant observations and tests into a reproducible record. Preserve timestamps and context while removing sensitive information. State whether the issue is resolved, mitigated, unresolved, or escalated, and name the next action or owner. Clearly mark any unperformed repair and remaining uncertainty.
- **Alt text:** Timestamped symptoms, brief evidence excerpts, and test results pass through redaction and context review into a troubleshooting record. The record includes impact, environment, timeline, expected and actual behavior, evidence, hypothesis, tests, actions, verification, status, and next owner. Resolved, mitigated, and escalated are alternative outcomes.
- **Prompt:** [03-generation-prompt.txt](03-generation-prompt.txt).
- **Review note:** The figure highlights three common statuses, not an exhaustive list or mandatory progression. An unresolved issue can stay under investigation. Preserve original evidence according to local rules; redact the shared copy rather than deleting or rewriting source logs. The student's 150–250 word note should identify instructor-provided evidence as synthetic and avoid claiming resolution without verification.
