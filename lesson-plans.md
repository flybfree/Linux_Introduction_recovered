# Lesson Plans — Introduction to Linux

These plans use the requested course template and align assigned textbook reading with the companion guide in [`book-alignment-linux-plus-xk0-005.md`](book-alignment-linux-plus-xk0-005.md). Chapter numbers refer to Ted Jordan and Sandor Strohmayer, *CompTIA Linux+ Certification All-in-One Exam Guide, Second Edition (Exam XK0-005)*. Read the course lesson first; assigned book selections reinforce that lesson. “Selected sections” means the topics listed, not every procedure or exercise in the chapter.

---

# Lesson 1 — Operating Systems and Linux Fundamentals

## ***Objectives:***

- Explain the purpose of an operating system and its roles in process, memory, filesystem, and device management.
- Describe the relationship among Unix, GNU, the Linux kernel, and Linux distributions.
- Identify the major layers of a Linux system and compare distribution purposes.
- Install or open Ubuntu in an approved lab environment and perform basic post-installation inspection.

## ***Lecture Topics:***

- Operating-system responsibilities; GUI and CLI use cases.
- Unix history, GNU, Linux kernel, distributions, open-source development, and system layers.
- Distribution families, package managers, support models, and selecting a distribution.
- Installation planning, virtual machines, storage safety, and initial system updates.

## ***Lab(s):***

- Open an approved Ubuntu VM or complete the instructor-approved Ubuntu installation activity.
- Identify the system and inspect basic hardware, memory, storage, and OS release information.
- Complete the approved post-installation update workflow and record observations.

## ***Instructor Preparation:***

- Prepare an Ubuntu Desktop LTS installer or prebuilt VM; verify the installation media and provide a rollback path.
- Use only computers or virtual disks approved for installation. Do not direct students to overwrite a personal or shared disk.
- Prepare a fallback VM and sample inspection output for students who cannot install locally.

## ***Student Preparation and Required Materials:***

- Read selection from McGraw-Hill text: **Chapter 1, “An Introduction to Linux and a Pre-Assessment Exam”; selected installation-planning sections of Chapter 20, “Installing and Configuring Linux.”** Chapter 18's virtualization overview is optional.
- Required materials: approved Ubuntu VM or installation environment, terminal access, and course worksheet.

## ***Assignments:***

- Submit a short system-identification worksheet with command output and a brief description of the kernel, distribution, and hardware observed.
- Complete the Lesson 1 knowledge check. Describe one installation-safety decision made during the lab.

---

# Lesson 2 — The Linux Command Line

## ***Objectives:***

- Explain the roles of terminal, shell, command, option, argument, and environment.
- Find command help in manual pages and built-in help.
- Use paths, variables, aliases, and command syntax accurately.
- Explain standard input, output, errors, pipelines, and exit status.
- Edit and source a user-level shell configuration in the practice environment.

## ***Lecture Topics:***

- Terminal versus shell; Bash prompt and command structure.
- Commands, options, arguments, quoting, whitespace, and help resources.
- Working directory, absolute and relative paths, variables, aliases, and shell configuration.
- Standard streams, redirection, pipes, command history, and reading command results.

## ***Lab(s):***

- Navigate a bounded practice directory and use help to investigate unfamiliar commands.
- Create and inspect variables and aliases, then safely edit and source a lab shell configuration file.
- Redirect output and errors and combine commands with a short pipeline.
- Inspect and terminate only a process started by the student as directed by the lesson.

## ***Instructor Preparation:***

- Provide an Ubuntu VM with Bash, `nano`, `man`, and `info` available.
- Prepare a disposable practice directory and demonstrate how to restore the original shell configuration if a student makes an error.
- Ensure process examples are student-owned and harmless.

## ***Student Preparation and Required Materials:***

- Read selection from McGraw-Hill text: **Chapter 3, “Working with the Linux Shell”; selected basic editing sections of Chapter 2, “Using the vi Text Editor.”**
- Required materials: terminal access, Bash, `nano` or another approved editor, and a practice directory.

## ***Assignments:***

- Submit a command-reference sheet with one example of help lookup, variable/alias use, redirection, and a pipeline.
- Complete the Lesson 2 knowledge check and identify one recovery step for a mistyped command or shell configuration change.

---

# Lesson 3 — Files, Directories, and Paths

## ***Objectives:***

- Explain the root directory, home directory, current working directory, and common FHS locations.
- Distinguish absolute and relative paths, hidden names, filename patterns, and symbolic links.
- Create, inspect, copy, move, rename, search, and remove files safely in a practice area.
- Use `less`, `head`, and `tail` to inspect text without editing it.
- Preview the effect of a file operation and verify its result.

## ***Lecture Topics:***

- Linux directory tree and common FHS locations, including `/etc`, `/home`, `/usr`, `/var`, `/tmp`, `/boot`, `/dev`, `/proc`, and `/sys`.
- Paths, quoting, spaces, hidden files, globs, file types, and links.
- Safe use of `touch`, `cp`, `mv`, `rm`, `rmdir`, `find`, `file`, `less`, `head`, and `tail`.
- Difference between viewing, editing, moving, and deleting data.

## ***Lab(s):***

- Create a small practice tree and inspect paths, file types, hidden files, and directory contents.
- Create, copy, move, and rename sample files; search for selected names and inspect sample text.
- Create and inspect a symbolic link, then remove only identified lab content.

## ***Instructor Preparation:***

- Provide an Ubuntu practice VM and a writable, disposable lesson directory with sample files.
- Demonstrate safe path completion and a preview/listing before any removal exercise.
- Keep all destructive operations inside the supplied practice tree.

## ***Student Preparation and Required Materials:***

- Read selection from McGraw-Hill text: **Chapter 5, “Managing Linux Files and Directories”; selected basic editing material from Chapter 2.** Chapter 13's `head` and `tail` sections are optional reinforcement.
- Required materials: terminal, file viewer/editor, and the lesson's practice directory.

## ***Assignments:***

- Submit a labeled filesystem tree and a short command log describing one copy/move/search operation and its verification.
- Complete the Lesson 3 knowledge check. Explain how the practice area prevented accidental deletion of unrelated files.

---

# Lesson 4 — Users, Groups, and Permissions

## ***Objectives:***

- Distinguish account names from UIDs and group names from GIDs.
- Read ownership and permission fields in a long listing.
- Explain file and directory read, write, and execute permissions.
- Inspect identity and group membership and change permissions on lab files.
- Explain how `umask`, special permission bits, and ACLs affect access.
- Describe why account and ownership changes require care.

## ***Lecture Topics:***

- Users, groups, UID/GID, ownership, and account databases.
- Permission triplets, symbolic and octal notation, and directory-specific behavior.
- `id`, `groups`, `ls -l`, `chmod`, `chown`, `chgrp`, and `umask`.
- Special permissions, ACL concepts, least privilege, and safe account administration.

## ***Lab(s):***

- Inspect the student's identity and create a private practice tree.
- Set and interpret symbolic and octal permissions on sample files and directories.
- Observe default permissions under the current `umask`; interpret instructor-provided ownership and ACL examples.
- Do not alter system accounts or shared directories.

## ***Instructor Preparation:***

- Provide a disposable Ubuntu VM and writable student-owned practice directories.
- Prepare sample `ls -l` and ACL outputs for environments where ACL tools or permissions vary.
- Keep account creation, ownership changes outside the student's directory, and system permission changes out of the student lab.

## ***Student Preparation and Required Materials:***

- Read selection from McGraw-Hill text: **Chapter 4, “Managing Linux Users and Groups,” and Chapter 6, “Managing Ownership and Permissions.”** Chapter 16's root-access overview is optional.
- Required materials: Ubuntu VM, terminal, and a practice directory; no administrator privileges are required for the core lab.

## ***Assignments:***

- Submit a permissions worksheet showing the before/after mode of lab files and explaining who can read, write, or execute them.
- Complete the Lesson 4 knowledge check and describe one case where granting broad access would be inappropriate.

---

# Lesson 5 — Processes and Job Control

## ***Objectives:***

- Distinguish a program, process, and shell job.
- Interpret process IDs, parent process IDs, users, and common process states.
- Inspect processes using `ps`, `top`, `htop`, `btop`, and `pgrep` where installed.
- Suspend, resume, foreground, and background a job in an interactive shell.
- Send a graceful termination request and explain why forceful termination requires caution.
- Explain process niceness and adjust only a process started for the lab.

## ***Lecture Topics:***

- Process creation, parent/child relationships, PID/PPID, process states, and system versus user processes.
- Process inspection and resource views; differences among `ps`, `top`, `htop`, and `btop`.
- Signals, `Ctrl+C`, `Ctrl+Z`, shell jobs, background/foreground control, and `kill`.
- Niceness and responsible process termination.

## ***Lab(s):***

- Start a harmless student-owned process and inspect it using process tools.
- Suspend, background, resume, and foreground the job; terminate it gracefully.
- Observe resource displays and adjust the niceness of only the student's own lab process.

## ***Instructor Preparation:***

- Confirm `ps`, `top`, `pgrep`, `htop`, and `btop` are installed or provide a prepared VM; do not make package installation part of the lab.
- Provide interactive Bash terminals and use a harmless process such as the supplied sleep example.
- Emphasize PID reuse and require process identity checks before any termination.

## ***Student Preparation and Required Materials:***

- Read selection from McGraw-Hill text: **Chapter 9, “Managing Linux Processes.”** Read scheduling sections with Lesson 7.
- Required materials: Ubuntu VM, interactive terminal, and `htop`/`btop` if available.

## ***Assignments:***

- Submit a process observation table with command, PID, state/resource observation, and safe termination result.
- Complete the Lesson 5 knowledge check and explain graceful versus forceful termination.

---

# Lesson 6 — Software and Package Management

## ***Objectives:***

- Explain packages, repositories, package indexes, and dependencies.
- Distinguish refreshing package metadata from upgrading installed software.
- Search for and inspect packages using APT and `dpkg`.
- Install and remove a small package in a disposable Ubuntu VM.
- Compare major Linux package-management families.
- Explain package provenance, transaction review, shared libraries, and rollback planning.

## ***Lecture Topics:***

- Package formats, repositories, metadata, dependencies, and trust.
- APT search, policy, install, remove, update, and upgrade operations.
- `dpkg` package status, file lists, and path ownership.
- Debian/Ubuntu, RPM/DNF, SUSE/ZYpp, and Arch package ecosystems.
- Universal package formats, source builds, shared libraries, and recovery planning.

## ***Lab(s):***

- Refresh package indexes and inspect package candidates and sources.
- Install a small instructor-selected package in a disposable VM; inspect its files and package ownership.
- Remove the lab package and verify the result; inspect a shared-library relationship using lesson instructions.
- Plan an update without applying unrelated changes.

## ***Instructor Preparation:***

- Provide a disposable Ubuntu VM snapshot and select a small package that is safe to install/remove.
- Confirm network/repository access or prepare representative package output.
- Explain which commands change state and require approval; students must review transaction prompts.

## ***Student Preparation and Required Materials:***

- Read selection from McGraw-Hill text: **Chapter 10, “Managing Linux Applications,” focusing on APT and `dpkg`.** Other package formats are comparison reading.
- Required materials: disposable Ubuntu VM, terminal, and network access to approved repositories or prepared sample output.

## ***Assignments:***

- Submit a package record with source, candidate version, dependencies, installed files, and removal verification.
- Complete the Lesson 6 knowledge check and write a short package update/rollback plan.

---

# Lesson 7 — Shell Scripting and Automation

## ***Objectives:***

- Explain the shebang, script interpreter, executable permission, and script exit status.
- Create, syntax-check, and run a Bash script.
- Use variables, quoting, parameters, input, conditions, loops, and functions.
- Explain how a user crontab schedules work and inspect/edit a harmless test entry.
- Plan and document a script that gathers system information without changing configuration.

## ***Lecture Topics:***

- Script files, shebangs, Bash invocation, executable permissions, and syntax checking.
- Variables, quoting, positional parameters, `read`, tests, conditions, loops, and functions.
- Standard output and error, exit status, safe input handling, and debugging.
- Time functions, timestamps, user crontabs, and schedule verification.

## ***Lab(s):***

- Build a system-information report script in the practice directory.
- Syntax-check, run, inspect, and extend the script with a loop.
- Add and remove one harmless user-level test schedule in the disposable VM, if cron is available.

## ***Instructor Preparation:***

- Provide Ubuntu VM, Bash, terminal editor, writable practice directory, and cron where scheduling is included.
- Prepare a safe sample script and a process for checking/removing the test schedule.
- Keep scripts read-only with respect to system configuration and prohibit `sudo` in the lab.

## ***Student Preparation and Required Materials:***

- Read selection from McGraw-Hill text: **Chapter 13, “Writing Shell Scripts”; selected Chapter 3 sections on time, environment, redirection, and pipes; selected Chapter 9 sections on cron, `at`, and systemd timers.**
- Required materials: Bash VM, text editor, terminal, and practice directory.

## ***Assignments:***

- Submit the documented report script, syntax-check result, sample report, and a brief explanation of its exit status.
- Complete the Lesson 7 knowledge check. If scheduling was completed, include evidence that the harmless entry was removed.

---

# Lesson 8 — Text Processing and Pipelines

## ***Objectives:***

- Explain how standard input, output, and error flow through pipelines and redirection.
- Filter and select fields with `grep`, `cut`, and introductory `awk`.
- Sort, count, and transform data with `sort`, `uniq`, `wc`, `tr`, and `sed`.
- Build and explain short pipelines one stage at a time.
- Save a report without overwriting source data and identify cases where simple tools do not safely parse CSV.

## ***Lecture Topics:***

- Pipeline data flow, redirection, and preserving original source files.
- Filtering, field selection, sorting, deduplication, counting, and character transformation.
- Introductory `awk`/`sed` operations and common quoting/field pitfalls.
- Verifying intermediate results and recognizing structured-data limitations.

## ***Lab(s):***

- Create a synthetic data file and inspect its structure.
- Build pipelines to filter, sort, count, transform, and select fields.
- Save a concise report to a new file and explain each pipeline stage.

## ***Instructor Preparation:***

- Prepare an anonymized synthetic dataset with clear fields and a few deliberate edge cases.
- Ensure standard GNU text utilities are available; provide expected intermediate output.
- Emphasize that source data must remain unchanged.

## ***Student Preparation and Required Materials:***

- Read selection from McGraw-Hill text: **Chapter 13 sections on processing text streams and the `tr`, `cut`, `sed`, `awk`, `sort`, `head`, `tail`, `uniq`, and `wc` commands; selected Chapter 3 sections on redirection and pipes.**
- Required materials: terminal, Bash, synthetic data file, and writable practice directory.

## ***Assignments:***

- Submit the source dataset, final report, and a short annotated explanation of each pipeline stage.
- Complete the Lesson 8 knowledge check, including a note about why simple field splitting may be unsuitable for quoted CSV.

---

# Lesson 9 — Storage, Filesystems, and Backups

## ***Objectives:***

- Distinguish storage devices, partitions, filesystems, and mount points.
- Inspect devices, metadata, mounts, and filesystem/directory usage.
- Explain LVM components, snapshots, thin provisioning, and common RAID layouts at a conceptual level.
- Create, list, extract, and verify a small compressed archive in a practice directory.
- Compare backup strategies and explain why backup copies need separation and restore tests.

## ***Lecture Topics:***

- Block devices, partition tables, filesystems, mounts, and FHS connections.
- LVM physical volumes, volume groups, logical volumes, and snapshot limitations.
- RAID levels, resilience and availability limits, and why RAID is not a backup.
- `df`, `du`, `tar`, archive verification, restore, and backup strategy.

## ***Lab(s):***

- Inspect device/filesystem information and current mounts without changing them.
- Compare filesystem capacity with directory usage.
- Create a small synthetic data set, archive it, list contents, restore into a separate practice folder, and verify the restored copy.
- Draft a backup plan for a sample use case.

## ***Instructor Preparation:***

- Provide Ubuntu VM and small non-sensitive sample files; confirm `tar`, `lsblk`, `df`, and `du` are available.
- Use read-only storage inspection. Do not require partitioning, formatting, mounting, unmounting, RAID, or LVM changes.
- Provide sample output if device metadata is restricted or virtualized.

## ***Student Preparation and Required Materials:***

- Read selection from McGraw-Hill text: **Chapter 7, “Managing Storage,” and Chapter 8, “Configuring Volume Management,” focusing on inspection, LVM/RAID concepts, archives, and backup strategy.**
- Required materials: Ubuntu VM, terminal, synthetic files, and writable practice directory with room for an archive and restored copy.

## ***Assignments:***

- Submit archive/listing/restore verification evidence and a backup plan identifying source, destination, frequency, protection, and restore test.
- Complete the Lesson 9 knowledge check. State one limitation of RAID or a same-disk copy as a backup.

---

# Lesson 10 — Networking Fundamentals

## ***Objectives:***

- Describe interfaces, addresses, subnets, routes, DNS, ports, and services.
- Distinguish link, Internet, transport, and application responsibilities.
- Inspect interfaces, addresses, routes, and local listening sockets.
- Perform bounded connectivity/name-resolution checks and an approved HTTPS request.
- Use evidence to distinguish local, routing, name-resolution, and application symptoms.

## ***Lecture Topics:***

- TCP/IP layers and basic client/server communication.
- Interfaces, IPv4/IPv6 addresses, prefixes, routes, gateways, and DNS.
- TCP/UDP, ports, sockets, listeners, and firewalls as a later security topic.
- `ip`, `ping`, `getent`/`resolvectl`, `ss`, and limited `curl` use.
- Limits of diagnostic responses and safe network-use boundaries.

## ***Lab(s):***

- Inspect local interfaces and routes; make a bounded loopback or approved gateway test.
- Resolve an instructor-approved hostname and inspect sockets.
- Request headers from an instructor-approved HTTPS endpoint and write a layered diagnosis.

## ***Instructor Preparation:***

- Prepare an Ubuntu VM, approved endpoint, and safe destination for limited tests.
- Account for network restrictions on ICMP, DNS, or Internet access; have sample output as fallback.
- Prohibit scans, probing classmates' devices, or sharing sensitive network details.

## ***Student Preparation and Required Materials:***

- Read selection from McGraw-Hill text: **Chapter 14, “Managing Linux Network Settings.”** Selected introductory encryption, SSH, and firewall concepts from Chapter 15 may be previewed before Lesson 12.
- Required materials: network-connected course VM or instructor-provided output, terminal, and approved endpoint information.

## ***Assignments:***

- Submit a short network evidence report distinguishing observations, likely layer, and remaining uncertainty.
- Complete the Lesson 10 knowledge check and explain why a failed ping alone does not prove a host is unavailable.

---

# Lesson 11 — Services and System Administration

## ***Objectives:***

- Distinguish a service, process, systemd unit, and unit file.
- Describe firmware-to-userspace boot stages and systemd's place in the sequence.
- Inspect systemd targets, service state, and logs using read-only commands.
- Read a simple unit file and distinguish start, restart, reload, and enable actions.
- Inspect guest-visible hardware, drivers, modules, sysfs, and udev without reconfiguration.
- Manage only the harmless course-provided user service in the lab.

## ***Lecture Topics:***

- Hardware discovery, PCI/USB, drivers, kernel modules, sysfs, device nodes, and udev.
- Firmware, bootloader, kernel, initramfs, root filesystem, and systemd startup.
- systemd unit types, dependencies, targets, service states, and journal evidence.
- Service control actions and safe boundaries for user versus system units.

## ***Lab(s):***

- Inspect the supplied user service before starting; start, inspect, restart, check logs, stop, and clean up that unit only.
- Inventory guest-visible hardware and inspect selected driver/module information.
- Inspect current boot/default-target evidence and write a short service and hardware report.

## ***Instructor Preparation:***

- Provide Ubuntu VM with active user systemd session, harmless Lesson 11 service unit, and `lspci`/`lsusb` utilities.
- Prepare sample hardware/journal outputs in case virtualization or access restrictions limit visibility.
- Do not ask students to change system services, drivers, bootloader settings, or device configuration.

## ***Student Preparation and Required Materials:***

- Read selection from McGraw-Hill text: **Chapter 11, “Managing the Linux Boot Process,” and Chapter 12, “Managing Hardware Under Linux.”** Selected Chapter 9 process material supports service concepts.
- Required materials: course VM with `systemd`, supplied harmless user unit, terminal, and hardware tools or sample output.

## ***Assignments:***

- Submit a service-state/log summary, a high-level boot sequence, and a guest hardware inventory with one stated limitation.
- Complete the Lesson 11 knowledge check and confirm the demonstration service is stopped after the lab.

---

# Lesson 12 — Security and Safe Administration

## ***Objectives:***

- Explain least privilege, authentication, authorization, and defense in depth.
- Distinguish ordinary and root access and evaluate privileged commands before use.
- Explain software provenance, trusted repositories, and timely updates.
- Describe account, password, SSH key, host-key, and firewall fundamentals.
- Complete a read-only security review and choose safe responses to risky scenarios.

## ***Lecture Topics:***

- Confidentiality, integrity, availability, identity, authentication, and authorization.
- Root, `sudo`, least privilege, and review of command scope/effects.
- Credentials and account protection; SSH user keys versus host keys.
- Repository trust, updates, software sources, listeners, firewall purpose, and backup recovery.
- Incident reporting and the safe-change routine.

## ***Lab(s):***

- Read-only identity/group, package source/update, and listener review in an approved VM.
- Analyze scenarios involving untrusted scripts, unexpected package changes, SSH host-key warnings, credential exposure, and firewall requests.
- Produce a concise security decision worksheet without changing system settings.

## ***Instructor Preparation:***

- Provide a course VM or anonymized screenshots; ensure the lab does not require `sudo` or system changes.
- Prepare safe scenario cards and the institution's actual incident-reporting path.
- Remove identifying account, package, host, and network details from projected examples.

## ***Student Preparation and Required Materials:***

- Read selection from McGraw-Hill text: **Chapter 16, “Securing Linux”; selected Chapter 15 sections on encryption, SSH, and firewalls; selected Chapter 6 permission review.**
- Required materials: approved VM or anonymized output, terminal, and scenario worksheet.

## ***Assignments:***

- Submit the read-only audit worksheet and safest-next-action rationale for each assigned scenario.
- Complete the Lesson 12 knowledge check and write a short least-privilege example from the lab context.

---

# Lesson 13 — Troubleshooting and Documentation

## ***Objectives:***

- Define a problem symptom, scope, and timeline in observable terms.
- Collect relevant read-only service, journal, process, storage, and network evidence.
- Separate observations, inferences, and testable hypotheses.
- Test one variable at a time, verify a result, and identify when to escalate.
- Write a reproducible troubleshooting record with evidence and next steps.

## ***Lecture Topics:***

- Standard troubleshooting model: define, scope, record, collect, hypothesize, test, verify, document.
- Choosing narrow evidence with `systemctl`, `journalctl`, `df`, `du`, `ps`, `ip`, and earlier network tools.
- Time ranges, log levels, access and retention limits, and safe handling/redaction of logs.
- Verification, uncertainty, escalation, and useful handoff notes.

## ***Lab(s):***

- Record a read-only system snapshot and inspect an approved harmless service if available.
- Diagnose synthetic service, storage, and name-resolution case bundles.
- Write a 150–250 word troubleshooting handoff note that separates evidence from inference.

## ***Instructor Preparation:***

- Prepare anonymized case bundles and sample outputs; do not create faults on student/shared systems.
- Ensure access to a harmless Lesson 11 user unit or provide output bundles.
- Remind students to redact personal/network data and follow institutional escalation procedures.

## ***Student Preparation and Required Materials:***

- Read selection from McGraw-Hill text: **Chapter 19, “Troubleshooting and Diagnostics.”** Selected Chapter 10 application-crash, Chapter 14 network, and Chapter 11 boot/service sections are useful supplements.
- Required materials: Ubuntu VM or supplied case packets, terminal, and troubleshooting worksheet.

## ***Assignments:***

- Submit three case analyses and one documented handoff note with impact, timeline, evidence, hypothesis, test, result, and next action.
- Complete the Lesson 13 knowledge check. Do not claim a root cause unless evidence supports it.

---

# Lesson 14 — Integration Capstone

## ***Objectives:***

- Combine course concepts in a bounded Linux project.
- Write and run a Bash workflow that produces a limited system report and archives synthetic practice data.
- Inspect, restore, and compare the archive in a safe project-local directory.
- Apply least privilege, privacy, and path-safety decisions.
- Verify acceptance criteria and document setup, results, limitations, and troubleshooting.

## ***Lecture Topics:***

- Project scope, input/output boundaries, acceptance criteria, and plan-before-build.
- Report generation with Bash and previously learned system-inspection commands.
- Archive creation, listing, restore, and comparison; distinction between a verification copy and independent backup.
- Security/privacy review, troubleshooting evidence, and technical handoff documentation.

## ***Lab(s):***

- Build the course capstone script in `~/linux-capstone/`; use only synthetic data in `demo-data/`.
- Syntax-check and run the script; inspect the report and archive listing.
- Restore to a project-local directory, compare against the source, and record verification evidence.
- Complete the security note, setup/run guide, troubleshooting record, and reflection.

## ***Instructor Preparation:***

- Provide an Ubuntu VM with Bash, GNU `tar`, a text editor, and writable home directory.
- Supply a synthetic example and a redacted completed-output sample.
- Keep projects local; do not request public upload of host-specific reports or archives.
- No administrator access, external repository, network listener, scheduled task, or system-wide change is needed.

## ***Student Preparation and Required Materials:***

- Read selection from McGraw-Hill text: **review selected Chapters 3, 7, 8, 13, 16, and 19** for shell composition, storage/backup, scripting, security, and troubleshooting. Chapters 17 and 18 are optional extensions.
- Required materials: Ubuntu VM, Bash, GNU `tar`, editor, terminal, and course capstone checklist.

## ***Assignments:***

- Submit the script, redacted report and archive listing, restore comparison result, setup/run guide, security note, troubleshooting record, and reflection.
- Complete the Lesson 14 knowledge check and peer review another project against its documented acceptance criteria.

