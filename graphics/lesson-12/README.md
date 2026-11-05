# Lesson 12 graphics

[Course graphics index](../README.md) · [Source chapter](../../lesson-12-security-safe-administration.md)

Production: AI-generated illustrations using the built-in image_gen tool, continuing the course's navy, teal, and amber style. All figures are drafts for instructional review. These explain concepts and decisions; the chapter's student lab remains read-only.

## Figure 12.1 — Identity, authentication, authorization

![An account identity is claimed, authentication supplies evidence of control, and authorization checks which actions are permitted. A normal student's example policy allows reading assigned course material but does not grant system-account changes. Least privilege limits access to the needed scope and duration.](01-identity-authentication-authorization.png)

- **Placement:** Section 2, after the explanation of least privilege and before sudo.
- **Learning purpose:** Separate proving identity from receiving permission to perform an action.
- **Caption:** **Figure 12.1.** Authentication establishes evidence for a claimed identity; authorization evaluates the actions that identity may perform. Least privilege limits the granted access to the task and its duration. Valid credentials alone do not grant every permission or make an operation safe.
- **Alt text:** An account identity is claimed, authentication supplies evidence of control, and authorization checks which actions are permitted. A normal student's example policy allows reading assigned course material but does not grant system-account changes. Least privilege limits access to the needed scope and duration.
- **Prompt:** [01-generation-prompt.txt](01-generation-prompt.txt).
- **Review note:** The access example is a fictional teaching policy, not a statement about every student account. Systems may combine permission bits, groups, ACLs, mandatory controls, delegated privileges, and other policy. An authentication mechanism offers evidence; it does not guarantee a person's intent or the safety of a subsequent command.

## Figure 12.2 — SSH verifies two different identities

![The client checks the server host-key fingerprint against independently trusted information. Separately, the user proves control of a private user key while the server verifies using an authorized public key. The private key is not sent. An unexpected host-key change calls for stopping and independent verification with the system owner.](02-ssh-user-and-host-identity.png)

- **Placement:** Section 5, after the host-key verification guidance and before the discussion of remote-access configuration changes.
- **Learning purpose:** Distinguish SSH user authentication from server identification and make the response to a changed host key clear.
- **Caption:** **Figure 12.2.** The server's host key helps the client identify the server. The user's key pair separately supports user authentication: the client proves control of its private key without transmitting it, and the server checks the proof using the authorized public key. Verify unexpected host-key changes through an independently trusted channel before proceeding.
- **Alt text:** The client checks the server host-key fingerprint against independently trusted information. Separately, the user proves control of a private user key while the server verifies using an authorized public key. The private key is not sent. An unexpected host-key change calls for stopping and independent verification with the system owner.
- **Prompt:** [02-generation-prompt.txt](02-generation-prompt.txt).
- **Review note:** The lanes separate roles rather than reproduce the SSH wire protocol or every authentication method. A fingerprint summarizes the host public key; the host private key also remains protected on the server. User authentication may instead use other policy-approved mechanisms. A legitimate rebuild can change a host key, but the warning is not evidence to accept it automatically. No live fingerprints or credentials are shown.

## Figure 12.3 — Make a deliberate administrative change

![Six stages identify the goal and target, inspect evidence, confirm authorization, make a minimal understood change, verify the result, and document the outcome. Unclear authority or impact calls for stopping, while unexpected results call for evidence preservation and an approved investigation or rollback plan.](03-safe-change-workflow.png)

- **Placement:** Section 9, after the paragraph summarizing identify, inspect, authorize, change, verify, document.
- **Learning purpose:** Turn the chapter's safe-change routine into a repeatable decision sequence.
- **Caption:** **Figure 12.3.** Identify the target, inspect the current state, confirm authorization, make the smallest understood change, verify with evidence, and document the outcome. Plan recovery before changes that affect data or access, and pause if the result differs from expectations. The student lab practices observation and decisions rather than system changes.
- **Alt text:** Six stages identify the goal and target, inspect evidence, confirm authorization, make a minimal understood change, verify the result, and document the outcome. Unclear authority or impact calls for stopping, while unexpected results call for evidence preservation and an approved investigation or rollback plan.
- **Prompt:** [03-generation-prompt.txt](03-generation-prompt.txt).
- **Review note:** “Authorize” means establish that the work is within the person's responsibility and applicable approval process; it does not mean every harmless inspection requires a formal ticket. Recovery plans vary by system. Suspected compromise follows the organization's incident procedure rather than ad hoc cleanup. The diagram introduces no commands or live administrative changes.
