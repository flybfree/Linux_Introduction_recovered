# Lesson 12 — Security and Safe Administration

## Lesson purpose

Linux security is a set of habits and controls that reduce risk over time. This lesson connects identity and permissions (Lesson 4), software sources and updates (Lesson 6), backups (Lesson 9), networking (Lesson 10), and services (Lesson 11). Students practice making deliberate administrative decisions, recognizing untrusted software and suspicious SSH prompts, and planning safe system changes.

## Learning objectives

By the end of the lesson, students will be able to:

- explain least privilege, authentication, authorization, and defense in depth;
- distinguish an ordinary user account from the root account and describe what `sudo` does;
- evaluate a privileged command before running it and identify unsafe command patterns;
- explain why supported software, trusted repositories, and timely updates matter;
- describe basic account, password, and SSH key protections;
- explain SSH host-key verification and choose a safe response to an unexpected fingerprint change;
- describe the role and limits of a host firewall; and
- complete a read-only security review and document sensible next steps.

## Instructor preparation

Use an Ubuntu Desktop LTS computer or disposable course VM. No student should enable a firewall, change SSH server settings, add repositories, install updates, or run a command with `sudo` as part of this lab. Prepare the scenario cards in the lab section and, if desired, screenshots of `id`, `groups`, `ss -tuln`, and a package's `apt policy` output. Remind students that local audit output can expose account names, package details, or network listeners; it should not be posted publicly.

## Suggested lesson sequence

| Segment | Time |
|---|---:|
| Connect prior lessons to security | 10 min |
| Textbook and guided decision demonstrations | 55 min |
| Student lab: read-only review and response scenarios | 45 min |
| Knowledge check and reflection | 20 min |

# Textbook chapter

## 1. Security is risk reduction

Computer security protects information and services from unauthorized access, unwanted changes, and disruption. A common model describes three goals: **confidentiality** (information is seen by authorized people), **integrity** (information remains accurate and trustworthy), and **availability** (systems and data are usable when needed). A control can help one goal while complicating another. For example, a firewall can reduce unwanted network access, but an overly broad rule can block legitimate work.

No single command makes a computer secure. **Defense in depth** uses multiple controls so that one failure does not expose everything: account permissions, software updates, trusted sources, backups, network filtering, and careful user decisions all contribute. Security also depends on the system's purpose and who administers it. A personal lab VM and a shared production server have different needs and change procedures.

## 2. Identity, authentication, and authorization

An **identity** is an account or other principal that a system can recognize. **Authentication** is evidence that a person or process is acting as that identity. A password, SSH key, or other approved factor can provide that evidence. **Authorization** is the decision about which actions the authenticated identity may perform. Authentication answers “who is this?”; authorization answers “what may this identity do?”

Linux assigns ownership and permissions to users and groups. Lesson 4 showed how those rules limit access to files and directories. A normal account should perform ordinary work with only the permissions it needs. The **root** account has broad control over the system; mistakes made with that authority can affect every user and system service.

The **principle of least privilege** means granting only the access needed, for only as long as needed. If a task can be completed in a user's own directory, it should not need administrator access. If an administrative task needs one privileged operation, avoid turning an entire interactive session into a root session.

![An account identity is claimed, authentication supplies evidence of control, and authorization checks which actions are permitted. A normal student's example policy allows reading assigned course material but does not grant system-account changes. Least privilege limits access to the needed scope and duration.](graphics/lesson-12/01-identity-authentication-authorization.png)

**Figure 12.1.** Authentication establishes evidence for a claimed identity; authorization evaluates the actions that identity may perform. Least privilege limits the granted access to the task and its duration. Valid credentials alone do not grant every permission or make an operation safe.

## 3. Use sudo deliberately

`sudo` requests that an allowed command run with elevated privileges, commonly as root. The system's sudo policy determines which users may run which commands. When authentication is required, the prompt normally asks for the invoking user's password, not the root password. The precise policy depends on system configuration. A successful password check does not mean that the requested command is harmless.

Before entering a password or confirming a privileged operation, pause and answer four questions:

1. **What exact command will run?** Read the command, options, paths, and any quoted or expanded values.
2. **Why does it need elevated access?** If the reason is unclear, stop and ask an instructor or administrator.
3. **What can it change?** Consider files, accounts, packages, services, network access, and other users.
4. **How will the result be checked or reversed?** Prefer a documented change plan and backup when appropriate.

The `-l` option asks `sudo` to list commands the current user may run under the policy. It does not run those listed commands:

```bash
sudo -l
```

Students should run this only on an approved lab system and should not share the output, which may reveal administrative policy. A password prompt is not a reason to type a password automatically. Unexpected prompts, unfamiliar commands, and commands copied from an untrusted source are reasons to stop.

Avoid broad patterns such as `sudo -i` for routine work, because a root shell makes every later command privileged. Avoid blindly approving `sudo` commands copied from a web page or chat. In particular, a command that downloads a script and immediately pipes it into a root shell gives that script broad authority before the user has inspected it. A short command is not necessarily a safe command.

Consider the difference between these examples:

```bash
apt policy curl
sudo apt install curl
```

The first inspects package information. The second changes system software and needs authorization, a trusted repository, and a plan for the change. A legitimate administrative task can still be inappropriate on a shared or managed computer.

## 4. Accounts and credentials

Use a separate account for each person so actions can be attributed and access can be removed without disrupting other users. Do not share accounts, passwords, or private SSH keys. Use a unique, hard-to-guess password or passphrase for each service, preferably stored in an approved password manager. Enable additional authentication factors where an organization provides them. Never send credentials in a message or place them in a script, source-code repository, or screenshot.

Lock the screen when stepping away from an unlocked computer, sign out of shared systems when finished, and report a lost device or suspected credential exposure promptly. A password change does not necessarily revoke every existing session or key; follow the service or organization's recovery process.

Groups provide a convenient way to grant shared access, but group membership is authority. Review whether membership is needed instead of adding accounts to a powerful group for convenience. The commands from Lesson 4 help examine the current identity and groups:

```bash
id
groups
```

They report information about the current account; they do not change account settings. Do not publish output from a real organization or shared host without permission.

## 5. SSH keys and host identity

**Secure Shell (SSH)** provides encrypted remote login and command access. There are two identities to consider in an SSH connection:

- The **user identity** proves which account is requesting access. SSH can use a password or a public/private key pair, depending on server policy.
- The **server host key** helps the client determine whether it is connecting to the expected server.

In a key pair, the public key may be installed on an authorized server; the private key stays with its owner and must be protected. A passphrase can add protection to a private key. Never email or upload a private key, and do not paste it into a website or terminal prompt. A host key is different from a user's public key: the host key identifies the server to the client.

On a first connection, SSH may ask whether to trust a server key. Verify the presented fingerprint using an independently trusted channel, such as the administrator's published instructions. On later connections, a warning that the host key changed could indicate a legitimate rebuild, but it could also indicate interception or that the address now reaches a different machine. Do not bypass the warning by deleting the saved key or accepting the new one automatically. Stop and verify with the system owner first.

![The client checks the server host-key fingerprint against independently trusted information. Separately, the user proves control of a private user key while the server verifies using an authorized public key. The private key is not sent. An unexpected host-key change calls for stopping and independent verification with the system owner.](graphics/lesson-12/02-ssh-user-and-host-identity.png)

**Figure 12.2.** The server's host key helps the client identify the server. The user's key pair separately supports user authentication: the client proves control of its private key without transmitting it, and the server checks the proof using the authorized public key. Verify unexpected host-key changes through an independently trusted channel before proceeding.

Enabling an SSH server, changing authentication policy, or editing firewall rules can lock out a remote administrator or expose a machine. These are planned changes: confirm that access works through an alternative console, validate the configuration, coordinate the change, and keep a rollback path. This course discusses the concepts but does not expose classroom computers to remote login.

## 6. Updates and software trust

Updates fix bugs and known vulnerabilities, but the source of an update matters. Lesson 6 introduced package managers and repositories. APT uses repository metadata and package signing mechanisms to help verify that packages came through configured, trusted sources and were not silently altered in transit. This verification depends on the repository configuration and trusted signing keys; it does not prove that every program is free of defects or appropriate for every purpose.

Prefer the distribution's official repositories or an organization-approved software source. Before installing software, ask who maintains it, how it receives security fixes, what permissions it needs, and whether the source is approved. Adding an unknown repository changes the set of parties that can offer software to the system. A downloadable installer or shell script from an unfamiliar site does not inherit the protections of the configured package sources.

APT commands already introduced in Lesson 6 can help with inspection and maintenance:

```bash
apt policy PACKAGE
apt list --upgradable
```

`apt policy` displays installed and candidate versions and the sources APT associates with a package. `apt list --upgradable` lists packages for which upgrades are available based on the local package lists. Output depends on the system's current metadata. These commands inspect; installing or upgrading packages is a separate, state-changing administrative task.

Keep supported releases and security updates current according to the computer's maintenance process. Ubuntu may apply some security updates automatically, but settings and coverage vary. On managed systems, follow the organization's schedule and testing requirements. For critical computers, plan updates, understand possible service interruption or reboot requirements, and maintain backups. Skipping security updates indefinitely leaves known weaknesses in place; applying changes without regard for service impact can also cause disruption.

## 7. Network exposure and firewalls

Lesson 10 introduced addresses, ports, and connections; Lesson 11 introduced services. A process may listen on a local or network socket. Whether another computer can reach it also depends on its bind address, routing, host firewall, network firewall, and other controls. A listener is evidence to investigate, not proof by itself of an exposure or vulnerability.

The `ss` command can display socket information:

```bash
ss -tuln
```

The options request TCP (`-t`) and UDP (`-u`) listening sockets (`-l`) with numeric addresses and ports (`-n`). Results can differ across systems and permissions. This is inspection only; it does not open or close ports. Do not assume a port number alone identifies the software or proves that a service is safe.

A **firewall** applies rules to network traffic. Ubuntu commonly offers UFW as a simplified host firewall interface, while other systems may use different tools and policies. A firewall should permit only traffic needed for the system's role, while preserving required access and considering both IPv4 and IPv6. A firewall does not repair vulnerable software, secure a stolen account, or replace authentication. Do not enable a firewall or alter rules on a classroom or managed system as part of this lesson; an incorrect rule can block legitimate access.

## 8. Backups, recovery, and response

Backups support availability and recovery from accidental deletion, hardware failure, ransomware, or a damaging configuration change. Lesson 9 covered backup and restore concepts. A backup is useful only if it is current, protected from the same failure as the original, and restorable. Sensitive backups need appropriate access controls and storage protection. Never treat a backup as permission to experiment on a production system.

When you suspect compromise, preserve information and follow the responsible organization's incident process. Report the time, symptoms, and actions already taken. Do not conceal the issue, delete logs, install a random “cleanup” tool, or wipe the system on your own. On a managed computer, contact the instructor or IT staff. On a personal lab VM, isolate it from networks only if the course procedure says how, then ask for guidance. A hurried response can destroy evidence or make recovery harder.

## 9. A repeatable safe-change routine

Before an administrative change, record the goal and current state. Check that the system is the intended target, consult trusted documentation, and verify that the operation is approved. Make the smallest change that addresses the need. Confirm the result with read-only evidence, record what changed, and know how to roll it back. For a change that could interrupt access or affect data, schedule it, notify affected users, and confirm a usable backup or recovery path.

The routine can be summarized as **identify, inspect, authorize, change, verify, document**. Not every task needs a formal change ticket, but every task benefits from clarity about target, authority, impact, and result.

![Six stages identify the goal and target, inspect evidence, confirm authorization, make a minimal understood change, verify the result, and document the outcome. Unclear authority or impact calls for stopping, while unexpected results call for evidence preservation and an approved investigation or rollback plan.](graphics/lesson-12/03-safe-change-workflow.png)

**Figure 12.3.** Identify the target, inspect the current state, confirm authorization, make the smallest understood change, verify with evidence, and document the outcome. Plan recovery before changes that affect data or access, and pause if the result differs from expectations. The student lab practices observation and decisions rather than system changes.

# Guided demonstrations

## Demonstration 1: Read before elevating

Display `apt policy curl` and explain that it inspects package versions and repository sources. Then show the text `sudo apt install curl` without running it. Ask learners what would change, which authorization is needed, and how they would confirm the package source and maintenance window. Emphasize that a command's familiarity does not substitute for understanding its effect.

## Demonstration 2: Recognize a dangerous pipeline

Show a fictional command of the form `curl ... | sudo bash` with a deliberately nonfunctional example URL. Do not execute it. Have students identify the download source, the pipe, the interpreter, and the elevated privilege. Ask what should be checked before any installation: source ownership, published instructions, signatures or checksums where applicable, permissions, and an approved recovery plan.

## Demonstration 3: Listener is not the whole exposure story

Show sample `ss -tuln` output from a prepared screenshot. Ask what is listening and what additional facts are needed: process identity, bind address, routing, firewall policy, service purpose, and authorization. Avoid showing real student addresses or services.

## Demonstration 4: SSH host-key warning

Present a mock SSH message warning that a server's host identification changed. Learners should stop, record the hostname and time, and verify the replacement fingerprint through a separate trusted channel with the administrator. They should not automatically remove the old known-hosts entry or turn off checking.

# Student lab — Read-only security review

## Safety boundaries

Use only the approved course VM or instructor-provided examples. The commands below inspect state. Do not run package installation or upgrade commands, add repositories, use `sudo` to change the system, enable a firewall, edit SSH configuration, or connect to an unknown server. Do not submit screenshots containing usernames, hostnames, addresses, or other identifying information unless the instructor directs you to do so.

## Part A: Inspect your current identity

Run:

```bash
id
groups
```

Record whether the output shows a user ID and group memberships. Explain why membership in an administrative group should be granted only when needed. Do not change group membership.

## Part B: Inspect package source information

Run:

```bash
apt policy curl
apt list --upgradable
```

If the package lists are missing or the output is unavailable, use the instructor's prepared screenshot. Identify the installed and candidate versions if shown, the source associated with the candidate, and one reason this output alone cannot guarantee that software is safe.

## Part C: Observe network listeners

Run:

```bash
ss -tuln
```

Select one row, if present. Identify the protocol and local address/port. State what additional evidence would be needed before deciding whether it should be reachable from another computer. If no rows appear, explain that an empty result is a snapshot, not a general proof that the computer has no network services.

## Part D: Security decision worksheet

For each situation, write the safest next action and the reason:

1. A website tells you to paste a command beginning with `curl` and ending with `sudo bash` to “fix” your computer.
2. A package installation prompt proposes removing several packages you did not expect.
3. SSH reports that a previously known server's host key has changed.
4. A classmate asks you to share your private SSH key so they can finish an assignment.
5. You discover an unfamiliar account in a screenshot from a shared server.
6. A request asks you to open a firewall port so a demo can be reached, but no one has confirmed the service, audience, or time window.
7. You suspect that you typed your password into a lookalike login page.
8. A system update is available during an active presentation on a shared computer.

Use the safe-change routine: identify the target, inspect the evidence, confirm authorization, choose the least disruptive safe action, verify, and document or report.

## Deliverables

Submit the three command observations (or instructor-provided alternatives) and the decision worksheet. Exclude identifying system details. No configuration changes are part of this lab.

# Knowledge check and answer key

1. **What is least privilege?** Granting only the access needed for a task and limiting it to the needed scope and duration.
2. **How do authentication and authorization differ?** Authentication establishes an identity; authorization determines what that identity may do.
3. **Why can root access make a small mistake serious?** Root can change or damage system-wide resources and affect other users and services.
4. **Does a `sudo` password prompt prove that the command is safe?** No. Authentication only confirms credentials according to policy; the user must still understand the command and its effects.
5. **What does `sudo -l` do?** Lists commands the current user is permitted to run through sudo under the active policy; it does not run those commands.
6. **Why should users avoid a persistent root shell for routine tasks?** Every subsequent command runs with broad privilege, increasing the impact of mistakes.
7. **Why is `curl ... | sudo bash` a high-risk pattern?** It sends downloaded content directly to a privileged shell without first inspecting and verifying the script.
8. **What does `id` report?** The current account's user and group identity information.
9. **What does `groups` report?** The groups associated with the current user.
10. **Why is administrative group membership sensitive?** It may grant authority to perform privileged operations.
11. **What does `apt policy PACKAGE` help inspect?** Installed/candidate version information and package source priorities/locations known to APT.
12. **What does `apt list --upgradable` list?** Packages for which the locally known package metadata indicates an upgrade is available.
13. **Does a trusted package signature prove that software has no vulnerabilities?** No. It helps establish package source/integrity within the trust configuration, not that the software is flawless or suitable.
14. **Why can adding an unknown repository be risky?** It adds another software source whose packages and signing keys may be trusted by the system.
15. **What does `ss -tuln` inspect?** TCP and UDP listening sockets using numeric addresses and ports.
16. **Does `ss -tuln` change firewall rules?** No; it displays socket information.
17. **What facts besides a listening socket affect network reachability?** Bind address, routing, host and network firewalls, and other access controls.
18. **What does an SSH user key pair do?** The private key proves the user's identity; a corresponding authorized public key lets the server verify that proof.
19. **What does an SSH host key help establish?** The identity of the remote server to which the client is connecting.
20. **What is the safe response to an unexpected SSH host-key change warning?** Stop and verify the new fingerprint with the system owner through a separate trusted channel.
21. **Should a private SSH key be sent to a classmate?** No. It must remain private; each user should have an independently authorized identity.
22. **What is a host firewall's role?** It filters network traffic according to rules; it does not replace updates, account security, or application security.
23. **Why should students not enable UFW on a shared classroom machine?** An incorrect change could disrupt connectivity or access, and the machine may be managed by someone else.
24. **Name two qualities of a useful backup.** It is current, protected from the same failure, and tested/restorable (any two).
25. **What should a student do after suspecting a credential was exposed?** Report promptly and follow the organization's recovery process; do not hide the event or improvise cleanup.
26. **What are the steps in the safe-change routine?** Identify, inspect, authorize, change, verify, and document.

# Chapter glossary

- **Authentication:** Establishing that a user or process is the identity it claims to be.
- **Authorization:** Determining which actions an authenticated identity may perform.
- **Confidentiality:** Protecting information from unauthorized disclosure.
- **Defense in depth:** Using multiple security controls so one failure does not defeat all protection.
- **Host key:** An SSH key used by a client to identify a server.
- **Integrity:** Protecting information and system state from unauthorized or accidental changes.
- **Least privilege:** Giving an identity only the access needed for its task.
- **Availability:** Keeping systems and data usable by authorized users when needed.
- **Private key:** The secret half of a public/private key pair; it must be protected.
- **Public key:** The shareable half of a key pair, used by a service to verify a cryptographic proof.
- **Root:** The Linux superuser identity with broad system privileges.
- **SSH:** Secure Shell, a protocol and toolset for encrypted remote access.
- **sudo:** A tool that requests policy-controlled execution of a command with another identity's privileges, commonly root.
- **Trust store/key:** Locally trusted cryptographic keys used to verify software or other identities.

# Instructor materials

## Facilitation notes

- Keep all demonstrations non-executing when a command could modify the host. Read the command aloud and have students explain each component.
- Distinguish package authenticity from software quality and safety; signatures establish a limited trust claim.
- Treat an SSH host-key warning as a verification task, not as a prompt to bypass security.
- If using real command output, remove usernames, hostnames, addresses, and institution-specific details.
- Adapt incident reporting to the institution's actual process; do not ask students to investigate a suspected compromise independently.

## Assessment rubric (10 points)

| Criterion | Points |
|---|---:|
| Correctly explains account identity and least privilege | 2 |
| Interprets package source/update output cautiously | 2 |
| Describes what a listener shows and what remains unknown | 2 |
| Chooses safe, evidence-based responses in at least six scenarios | 3 |
| Omits identifying details and documents observations clearly | 1 |

## References

- [Ubuntu security updates](https://documentation.ubuntu.com/security/security-updates/)
- [Ubuntu automatic updates](https://ubuntu.com/server/docs/how-to/software/automatic-updates/)
- [Ubuntu package management](https://ubuntu.com/server/docs/how-to/software/package-management/)
- [Ubuntu `sudoers` manual](https://manpages.ubuntu.com/manpages/noble/man5/sudoers.5.html)
- [Ubuntu firewall documentation](https://ubuntu.com/server/docs/firewalls/)
- [Ubuntu security suggestions](https://ubuntu.com/server/docs/explanation/security/security_suggestions/)
- [OpenSSH manual pages](https://www.openssh.com/manual.html)
