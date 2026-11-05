# Lesson 13 — Troubleshooting and Documentation

## Lesson purpose

Linux troubleshooting is a disciplined way to move from a reported symptom to a supported explanation and a safe next step. This lesson brings together command-line inspection, processes, storage, networks, services, logs, and text processing from earlier chapters. Students practice separating observations from assumptions, testing one hypothesis at a time, and writing a concise record another person can follow.

## Learning objectives

By the end of the lesson, students will be able to:

- define a symptom in observable terms and establish its scope and timeline;
- collect relevant, read-only evidence before making a change;
- use service status, journal, process, storage, and network output to test a hypothesis;
- distinguish a direct observation from an inference and identify remaining uncertainty;
- change one variable at a time in an authorized environment and verify the result; and
- write a useful troubleshooting record with reproducible steps, evidence, outcome, and next action.

## Instructor preparation

Use an Ubuntu Desktop LTS VM or provide the instructor-generated output bundles below. The lab is designed to be read-only on student systems. Do not create a real service failure, fill a disk, interrupt network connectivity, or change system settings on a student or shared computer. Ensure students can inspect a harmless user service from Lesson 11 if available. Prepare anonymized case output for any environment where journal permissions or service names differ. Remind students to redact usernames, hostnames, addresses, tokens, and other identifying data before sharing logs.

## Suggested lesson sequence

| Segment | Time |
|---|---:|
| Troubleshooting method and evidence quality | 25 min |
| Guided demonstrations: logs and cross-checks | 35 min |
| Student lab: diagnose scenario bundles | 50 min |
| Knowledge check and written reflection | 20 min |

# Textbook chapter

## 1. Troubleshooting starts with a symptom

A **symptom** is an observable indication that something is not working as expected. “The computer is broken” is too broad to investigate. “The course web page returned a timeout at 10:15, but the local desktop remained responsive” is more useful because it states what happened, when it happened, and what still worked.

Begin by asking what task failed, who or what is affected, when the problem began, whether it is repeatable, and whether anything changed shortly beforehand. Establish the **scope**: one user, one application, one machine, or multiple systems? Determine whether the problem is continuous or intermittent. If it is intermittent, note the time and conditions of each occurrence. Do not assume that the most recent change caused the problem; record it as a lead to investigate.

## 2. A repeatable investigation method

Use this sequence to keep an investigation focused:

1. **Define** the symptom in observable terms.
2. **Scope** the affected users, machines, functions, and time period.
3. **Record** relevant recent changes and the last known good state.
4. **Collect** read-only evidence from the narrowest useful source.
5. **Form** a testable hypothesis that explains the observations.
6. **Test** one variable at a time, using an authorized and low-risk check.
7. **Compare** the result with the prediction and update the hypothesis.
8. **Resolve or escalate** with evidence, then verify normal behavior.
9. **Document** the outcome, remaining uncertainty, and any follow-up.

The process is iterative. A test that disproves a hypothesis is useful: it narrows the search. Avoid changing several settings at once, because then a success or failure does not reveal which change mattered. Do not “try random commands” as a substitute for investigation. Some commands that look diagnostic can change state, remove evidence, interrupt a service, or expose data.

![Define a symptom, scope impact, record changes, and collect evidence before forming a hypothesis and prediction. Run one authorized low-risk test, compare the result, and revise when unsupported or inconclusive. Supported explanations lead to an authorized remedy or escalation, original-task verification, and documentation.](graphics/lesson-13/01-investigation-loop.png)

**Figure 13.1.** Define and scope the symptom, collect evidence, and test a prediction one variable at a time. Results may support, weaken, or leave a hypothesis unresolved. Revise as needed, resolve or escalate within authorization, verify the original task, and document uncertainty as well as outcomes.

## 3. Observations, inferences, and hypotheses

An **observation** is directly shown by evidence: “`systemctl status` reports the unit as failed” or “`df -h` reports that the filesystem is 98% used.” An **inference** is a conclusion drawn from one or more observations: “The service may have failed because it could not write a file.” A **hypothesis** is an explanation that can be tested: “The service's data filesystem may be full; checking its usage and the service log should support or weaken this explanation.”

Keep these categories separate in notes. A log line that says “permission denied” is an observation. The claim “the group owner is wrong” is an inference that needs inspection. A timestamp close to a failure is a clue, not proof of cause. Use exact excerpts and timestamps where appropriate, but include enough surrounding context to avoid changing a message's meaning.

![A synthetic service log reports Permission denied and the unit reports failed. These observations support an access-related inference, not a confirmed claim that group ownership is wrong. A testable hypothesis about the service identity's access leads to inspecting the configured path, identity, and path permissions.](graphics/lesson-13/02-observation-inference-hypothesis.png)

**Figure 13.2.** A permission-denied message is an observation. An access-related explanation is an inference until checked. Make the hypothesis testable by identifying what should be inspected next: the intended path, service identity, and applicable access controls. Do not broaden permissions based on a guess.

## 4. Choose evidence that answers a question

Collect the smallest relevant evidence set. Use the symptom to choose a subsystem rather than dumping every log and command output. Questions help select tools:

| Question | Useful read-only evidence |
|---|---|
| Is a service active or failed? | `systemctl status UNIT --no-pager` |
| What did the service log near the incident? | `journalctl -u UNIT --since "1 hour ago" --no-pager` |
| Are there recent warnings or errors? | `journalctl --since "1 hour ago" -p warning --no-pager` |
| Is a filesystem almost full? | `df -h` and, for a known directory, `du -h --max-depth=1 PATH` |
| What processes are using resources? | `ps aux` or `top` (introduced earlier) |
| What addresses and routes are configured? | `ip address` and `ip route` |
| Is the system under resource pressure? | `uptime` and `free -h` |

Command output can be incomplete. A user's journal view may omit system messages; a service may log to a file or external system instead. The system clock may differ from the time zone in a report. A process can exit before inspection. A command can show the current state, not necessarily the state at the time of failure. Include these limits when they matter.

## 5. Check service state and logs

Lesson 11 introduced `systemctl` and `journalctl`. Use inspection forms to ask about a service without restarting or changing it:

```bash
systemctl status UNIT --no-pager
systemctl is-failed UNIT
journalctl -u UNIT -n 30 --no-pager
journalctl -u UNIT --since "1 hour ago" --no-pager
```

Replace `UNIT` with the exact unit name supplied by the instructor. `status` gives a human-readable state and recent log lines; `--no-pager` keeps the output in the terminal instead of opening an interactive pager. `is-failed` checks whether the unit is in the failed state. `journalctl -u` filters journal entries for a unit; `-n 30` limits the result to recent entries, while `--since` restricts it to a time range. Journal retention and access permissions vary, so a missing message is not proof that an event did not happen.

Read timestamps and severity carefully. A warning is not always the cause of a failure, and an error may be expected or unrelated. Compare the relevant time window with the reported incident. If a service is running now, it may still have failed earlier or be providing degraded results. State is one piece of evidence, not a complete diagnosis.

For a user service, the user manager's journal can be queried with `journalctl --user -u UNIT`. Do not add `sudo` just because a system log is unavailable. Follow the lab's access rules or ask the instructor for an approved output bundle.

## 6. Check system resources and processes

Storage pressure can prevent applications from writing data, creating temporary files, or updating state. `df -h` reports filesystem capacity in human-readable units. `du -h --max-depth=1 PATH` summarizes space used by immediate subdirectories below a specified path; it may take time on large trees and may not include files the user cannot access. Begin with an appropriate known path rather than scanning the whole system.

High CPU or memory use may make a system slow, but a high value alone does not establish a fault. `ps aux` provides a process snapshot; `top` provides a changing view. `uptime` reports system uptime and load averages, which need context such as the number of CPU cores and the recent workload. `free -h` summarizes memory and swap; Linux uses otherwise available memory for caching, so “used” memory alone is not evidence that memory is exhausted.

Do not terminate a process simply because it appears near the top of a list. Identify its owner, role, and relationship to the symptom. Some processes are essential; stopping them can cause data loss or interrupt other users. In this lesson, process inspection is read-only.

## 7. Check networking one layer at a time

For a network symptom, begin at the failing task and work outward. Confirm whether the problem is limited to one application, one host, or several destinations. Use `ip address` to inspect interfaces and addresses, and `ip route` to inspect route entries. These commands report current configuration; they do not test whether a remote system responds.

Lesson 10's bounded checks can add evidence: `getent hosts NAME` queries configured name resolution; a limited `ping -c 4 DESTINATION` checks for ICMP echo replies; and a request to an instructor-approved endpoint can test application access. Each result has limits. Name lookup may use local caches or files, a firewall may block ping, and a web response only confirms that one request reached a service at that time. Do not scan classmates' systems or use destinations without authorization.

Compare tests that differ by one variable. For example, a successful request by IP address but failed request by hostname points toward a name-resolution issue, while still leaving possible proxy or application differences. Record precisely which name, address, protocol, and time were tested.

## 8. Work with logs responsibly

Logs are records produced by the kernel, system services, and applications. They may include account names, addresses, file paths, request details, or secrets accidentally written by software. Share only the portion needed for the investigation, redact sensitive values, and preserve timestamps and enough context. Do not publish an entire log bundle in a public forum.

Text-processing tools from Lesson 8 can help narrow a large output. For example, a pipe can send output to `grep` to select matching lines, or to `head` to show an initial sample. Filtering can hide context, so first note the original time range and source; then inspect nearby entries before drawing a conclusion. Avoid editing or deleting logs during investigation. On a managed system, follow retention and incident-response rules.

## 9. Verification and escalation

After an authorized repair, repeat the original test under comparable conditions. Confirm that the intended function works, check that the system remains stable, and make sure the repair did not create a new problem. “The error message disappeared” is not always enough; use the user's original task as the acceptance check.

Escalate when the system is shared or production-critical, the proposed change could affect security or availability, the evidence suggests compromise or data loss, the next step needs privileges you do not have, or the cause remains uncertain after reasonable checks. Provide a concise problem statement, impact, timeline, relevant observations, steps already taken, results, and the decision or help needed. Do not conceal uncertainty or claim a root cause that the evidence does not support.

## 10. Write a troubleshooting record

A useful record lets another person understand what happened and reproduce the reasoning. Include:

- **Title and impact:** a short description of the affected function and who is affected;
- **Environment:** relevant OS, service, or device details, without unnecessary personal data;
- **Timeline:** first occurrence, last known good state, and important changes;
- **Expected and actual behavior:** what should happen and what happened instead;
- **Evidence:** commands or checks, timestamps, and brief relevant output;
- **Hypothesis and test:** what explanation was considered and how it was checked;
- **Action and verification:** authorized change, result, and confirmation of the original task; and
- **Status and follow-up:** resolved, mitigated, unresolved, or escalated, plus the next owner/action.

Use neutral, specific language. Write “The service status was failed at 14:22” instead of “systemd is broken.” Include exact commands when safe and relevant, but remove secrets and identifiers. Clearly label synthetic or instructor-provided evidence. Good documentation is part of the technical solution: it prevents repeated work and helps future administrators recognize patterns.

![Timestamped symptoms, brief evidence excerpts, and test results pass through redaction and context review into a troubleshooting record. The record includes impact, environment, timeline, expected and actual behavior, evidence, hypothesis, tests, actions, verification, status, and next owner. Resolved, mitigated, and escalated are alternative outcomes.](graphics/lesson-13/03-evidence-to-handoff.png)

**Figure 13.3.** Turn relevant observations and tests into a reproducible record. Preserve timestamps and context while removing sensitive information. State whether the issue is resolved, mitigated, unresolved, or escalated, and name the next action or owner. Clearly mark any unperformed repair and remaining uncertainty.

# Guided demonstrations

## Demonstration 1: From vague report to testable question

Start with: “The app is broken.” Ask for expected behavior, exact error, scope, start time, repetition, and last known good state. Rewrite it as a concise symptom statement, such as: “At 14:10, user A's local demo page stopped responding on the course VM; the browser reports a connection refusal, while other local applications still open.” Mark which details are observations and which still need checking.

## Demonstration 2: Correlate service status with a narrow log window

Use instructor-prepared output for a harmless user unit. Show `systemctl --user status UNIT --no-pager` and `journalctl --user -u UNIT --since "1 hour ago" --no-pager`. Identify state, timestamps, and one relevant log line. Explain why the line supports a hypothesis without proving it. Do not restart a unit to make the demonstration look successful.

## Demonstration 3: Disk symptom and scope

Show sample `df -h` output where one filesystem is nearly full. Compare it with `du -h --max-depth=1 ~/` output. Explain that one reports filesystem capacity and the other summarizes directory usage, and that permissions, mount boundaries, and hidden or open-deleted files can affect interpretation. Do not delete files during diagnosis.

## Demonstration 4: Separate network layers

Present a case where an approved hostname fails to resolve but an instructor-provided application test by address succeeds. Have students state the evidence and a tentative name-resolution hypothesis. Point out that the tests may differ in more than one condition; the next test should isolate the variable rather than assume proof.

# Student lab — Diagnose from evidence

## Safety boundaries

Use the instructor's anonymized evidence bundles or read-only output from an approved VM. Do not stop/restart services, kill processes, remove files, change routes, alter permissions, install packages, or edit configuration. Never use `sudo` to obtain broader logs unless the instructor explicitly provides a disposable environment and directs the action. Redact identifying details from submissions.

## Part A: Record a live system snapshot

Run these inspection commands in the approved VM:

```bash
date --iso-8601=seconds
uptime
free -h
df -h
ip address
ip route
```

If a command is unavailable or output is restricted, record that and use instructor-provided output. Choose one observation related to system time, load, memory, storage, addresses, or routes. State exactly what it shows and one thing it does not prove.

## Part B: Review one approved service

If Lesson 11's user service is available, substitute its exact name:

```bash
systemctl --user status lesson11-course-demo.service --no-pager
journalctl --user -u lesson11-course-demo.service -n 20 --no-pager
```

If it is unavailable, use the instructor's sample. Record the reported state and whether the log contains relevant entries. Do not start or stop the unit. If you cannot access a service, explain the limitation rather than using elevated access.

## Part C: Diagnose case bundles

For each case, write (a) symptom, (b) observations, (c) hypothesis, (d) one low-risk next test, (e) a change that would be premature, and (f) whether to escalate.

### Case 1: Service unavailable

```text
Reported: The local demonstration did not load at 10:42.
systemctl status: Active: failed (Result: exit-code)
Journal: 10:41:58 demo.service: Failed to open /home/student/demo/data.db: Permission denied
Journal: 10:41:58 demo.service: Main process exited, status=1/FAILURE
```

Do not conclude immediately that the file permissions are the root cause. Consider confirming the unit's configured path and the file's ownership and access in the approved lab, then ask whether a recent change occurred. Never make the file world-writable as a quick fix.

### Case 2: Storage warning

```text
Reported: A report could not be saved at 13:05.
df -h: /dev/vda2  20G  19G  300M  99%  /
df -h: /dev/vda3  80G  22G   54G  29%  /home
Application: Save failed: No space left on device
```

State which filesystem appears constrained and what further check could identify where its space is used. Do not delete files, caches, or logs during the lab.

### Case 3: Name lookup failure

```text
Reported: The course site did not open by its usual hostname at 09:18.
ip address: interface has an assigned address and state UP
ip route: default route is present
getent hosts course.example.invalid: no output
ping -c 4 approved-gateway: 4 replies received
```

Explain what works and what remains uncertain. A gateway reply does not prove public Internet access, and the `.invalid` name in this synthetic case is intentionally not a real course site. Propose an approved name-resolution check before concluding the cause.

## Part D: Write a handoff note

Choose one case and write a 150–250 word troubleshooting record using the template. Separate facts and interpretation, include a next action, and avoid claiming resolution unless you have evidence.

```text
Title / impact:
Environment:
Timeline / last known good:
Expected vs actual:
Observations and evidence:
Hypothesis:
Test and result:
Action / verification:
Status / next owner:
```

## Deliverables

Submit Part A observations, the three case analyses, and one handoff note. Omit secrets, usernames, hostnames, addresses, and any unrelated log content. The lab does not require system changes.

# Knowledge check and answer key

1. **Why should a symptom be specific?** Specific, observable details establish scope and help select meaningful tests.
2. **What should be recorded before changing a system?** Current state, impact, timeline, recent changes, and relevant evidence.
3. **What is the difference between an observation and an inference?** An observation is directly shown by evidence; an inference interprets that evidence.
4. **Why test one variable at a time?** It helps identify which condition caused a changed result and avoids confounding multiple changes.
5. **What does `systemctl status UNIT --no-pager` show?** Human-readable runtime status and recent log information for the unit without launching a pager.
6. **What does `systemctl is-failed UNIT` check?** Whether the named unit is in the failed state.
7. **What does `journalctl -u UNIT -n 30 --no-pager` display?** Up to 30 recent journal entries associated with the unit without opening a pager.
8. **What does `journalctl --since "1 hour ago" -p warning --no-pager` filter for?** Journal entries in the selected time range at warning priority and more severe levels.
9. **Does a missing journal entry prove an event did not occur?** No. Access, retention, logging configuration, and time selection may limit what is visible.
10. **What does `df -h` report?** Filesystem capacity and usage in human-readable units.
11. **What does `du -h --max-depth=1 PATH` summarize?** Space used by immediate subdirectories under the specified path, in human-readable units.
12. **Why should you not delete files just because `df -h` shows little free space?** First identify the constrained filesystem and what uses its space; deletion can remove important data or evidence.
13. **What does `ip address` show?** Current network interfaces and assigned addresses.
14. **What does `ip route` show?** Current routing entries, including any default route.
15. **Does a default route prove Internet connectivity?** No. The route may exist while the gateway, upstream network, DNS, or application is unavailable.
16. **What does `uptime` help assess?** How long the system has been running and its load averages.
17. **Why is high load not by itself proof of a fault?** It requires context such as CPU count, workload, and whether users observe impact.
18. **Why can high “used” memory in `free -h` be misleading?** Linux uses available memory for caches; the available-memory and swap context matters.
19. **Why should an unfamiliar high-CPU process not be killed immediately?** It may be essential or belong to another user; identify its role and impact first.
20. **Why redact logs before sharing?** Logs may contain account names, addresses, paths, request details, or secrets.
21. **A ping to the gateway succeeds. What does this prove?** Only that the tested ICMP exchange with that gateway succeeded at that time; it does not prove Internet or application access.
22. **What should a useful troubleshooting note contain?** Impact, environment, timeline, expected/actual behavior, evidence, hypothesis, tests, actions, verification, and next steps.
23. **When should a student escalate?** When changes could affect shared systems, security or availability, data may be lost, privilege is required, compromise is suspected, or evidence remains inconclusive.
24. **Why is “The service is broken because permissions are wrong” a weak report based on a `Permission denied` log alone?** The log supports a permissions-related hypothesis but the exact path, ownership, access, configuration, and cause still need verification.

# Chapter glossary

- **Evidence:** Observations, outputs, or records used to support or challenge an explanation.
- **Hypothesis:** A testable explanation for observed behavior.
- **Inference:** A conclusion drawn from evidence but not directly observed.
- **Journal:** The log store used by systemd services and other system components on typical Ubuntu systems.
- **Load average:** A summary of runnable and uninterruptible task activity over recent time intervals; interpretation depends on system capacity and workload.
- **Root cause:** An underlying factor whose correction prevents or reduces recurrence; it should not be claimed without sufficient evidence.
- **Scope:** The users, systems, services, or functions affected by a problem.
- **Symptom:** An observable indication that expected behavior differs from actual behavior.
- **Troubleshooting:** A structured process for investigating, testing, resolving, and documenting a problem.
- **Verification:** A check that confirms whether the intended outcome was achieved under suitable conditions.

# Instructor materials

## Facilitation notes

- Reward disciplined uncertainty. A plausible hypothesis is not a confirmed cause.
- Ask students to state what a command output proves and what it cannot prove.
- Use anonymized or synthetic logs; never project secrets or personal network details.
- Case bundles are intentionally limited. Students should request the next useful evidence rather than inventing missing facts.
- Do not seed faults by filling disks, breaking network settings, changing ownership, or stopping services on student/shared devices.
- If an instructor chooses a controlled fault demonstration, use a disposable VM snapshot, obtain institutional authorization, and provide a tested rollback plan.

## Assessment rubric (10 points)

| Criterion | Points |
|---|---:|
| Symptom, impact, and timeline are specific | 2 |
| Observations are separated from inference | 2 |
| Hypotheses and next tests are relevant and low risk | 2 |
| Case conclusions acknowledge uncertainty and escalation needs | 2 |
| Handoff note is reproducible, concise, and redacted | 2 |

## References

- [Ubuntu `journalctl` manual](https://manpages.ubuntu.com/manpages/noble/man1/journalctl.1.html)
- [Ubuntu `systemctl` manual](https://manpages.ubuntu.com/manpages/noble/man1/systemctl.1.html)
- [Ubuntu tutorial: viewing and monitoring log files](https://ubuntu.com/tutorials/viewing-and-monitoring-log-files)
- [Ubuntu Server terminal tutorial](https://ubuntu.com/server/docs/tutorial/welcome-to-the-terminal/)
