# Introduction to Linux — 14-Lesson Course Progression

This roadmap turns Lesson 1 into a complete course sequence. Each lesson is designed as an independent module with a narrative textbook chapter, guided demonstrations, a hands-on lab, a knowledge check, a glossary, instructor materials, and a Moodle question bank.

## Course arc

The sequence moves from orientation and safe system use toward administration, automation, networking, and a practical capstone. Commands are introduced gradually; later lessons assume the concepts and command-line habits established earlier.

| Lesson | Title | Main questions | Practical outcome |
|---:|---|---|---|
| 1 | Operating Systems and Linux Fundamentals | What is an OS, and how is a Linux system assembled? | Install or open Ubuntu safely; identify the system and complete basic inspection. |
| 2 | The Linux Command Line | How do I navigate and work efficiently in a shell? | Navigate the filesystem, create and inspect files, use command help, and build command-line confidence. |
| 3 | Files, Directories, and Paths | How does Linux organize data? | Work with absolute and relative paths; copy, move, rename, remove, and search for files safely. |
| 4 | Users, Groups, and Permissions | Who may access a resource, and why? | Create and inspect identities; interpret and change ownership and permission settings. |
| 5 | Processes and Job Control | What is running, and how do I control it? | Inspect processes, manage foreground/background jobs, and terminate processes responsibly. |
| 6 | Software and Package Management | How does Linux install and maintain software? | Search repositories, install and remove packages, inspect package files, and apply updates. |
| 7 | Shell Scripting and Automation | How can repeated work become a reliable procedure? | Write, run, test, and safely schedule small Bash scripts using variables, conditions, loops, and exit status. |
| 8 | Text Processing and Pipelines | How can text output become useful information? | Combine commands with pipes and redirection; filter, transform, and summarize structured text. |
| 9 | Storage, Filesystems, and Backups | How is data stored, mounted, and protected? | Inspect storage, understand mounts, compare filesystem concepts, and create a safe backup plan. |
| 10 | Networking Fundamentals | How does a Linux system communicate? | Inspect interfaces and routes, test connectivity, resolve names, and explain common network layers. |
| 11 | Services and System Administration | How do background services start, run, and fail? | Inspect service state and logs, manage a non-destructive lab service, and identify startup behavior. |
| 12 | Security and Safe Administration | How do we reduce risk while operating a Linux system? | Apply least privilege, use `sudo` carefully, recognize unsafe commands, and establish update and account habits. |
| 13 | Troubleshooting and Documentation | How do I investigate a problem instead of guessing? | Follow a repeatable diagnostic process using symptoms, logs, system state, tests, and written evidence. |
| 14 | Integration Capstone | How do the pieces work together on a real task? | Build, document, inspect, secure, and troubleshoot a small Linux service or automation project. |

## Detailed lesson progression

### Lesson 1 — Operating Systems and Linux Fundamentals

Use the existing prototype as the course foundation. Students learn the roles of an operating system, the history of Unix, GNU, and the Linux kernel, Linux system layers, distributions, installation safety, and basic system inspection. The command set remains deliberately small: `whoami`, `hostname`, `uname`, `lscpu`, `free`, `lsblk`, `mkdir`, `cat`, and the initial APT update workflow.

### Lesson 2 — The Linux Command Line

Build a mental model of the terminal, shell, command structure, options, arguments, standard output, and exit status. Introduce `pwd`, `ls`, `cd`, `man`, `--help`, `clear`, `history`, `echo`, and `printf`. The lab should be entirely safe in a student-owned practice directory and should include recovering from a mistyped command.

### Lesson 3 — Files, Directories, and Paths

Develop filesystem fluency and explain common FHS locations such as `/etc`, `/home`, `/usr`, `/var`, `/tmp`, `/boot`, `/dev`, `/proc`, and `/sys`, including distribution variations. Use `touch`, `cp`, `mv`, `rm`, `rmdir`, `find`, `file`, `less`, and `head`/`tail`. Emphasize quoting, spaces in names, hidden files, glob patterns, and the difference between removing an empty directory and removing files. Every destructive example should use disposable lab data.

### Lesson 4 — Users, Groups, and Permissions

Explain identities, ownership, read/write/execute permissions, octal notation, and special considerations for directories. Introduce `id`, `groups`, `ls -l`, `chmod`, `chown`, `chgrp`, and `umask`, with account-creation demonstrations restricted to an approved VM. Students should practice permissions without weakening the host system.

### Lesson 5 — Processes and Job Control

Connect Lesson 1's process concept to live administration. Cover process IDs, parents, signals, foreground and background jobs, and resource inspection with `ps`, `top` or `htop`, `jobs`, `bg`, `fg`, `kill`, and `pgrep`. The lab should use student-started processes and distinguish graceful termination from forceful termination.

### Lesson 6 — Software and Package Management

Deepen the introductory APT material and compare Debian packages with RPM-based and Arch-based ecosystems. Cover repositories, package metadata, dependencies, upgrades, and removal using `apt`, `apt-cache` or `apt search`, and `dpkg` in inspection-oriented examples. Include a rollback or recovery discussion and explain why random installer scripts are risky.

### Lesson 7 — Shell Scripting and Automation

Move from one-line commands to repeatable programs. Teach shebangs, executable permissions, variables, quoting, positional parameters, conditions, loops, functions, exit status, and introductory user-level cron scheduling. Use `bash`, `read`, `crontab -l`, `crontab -e`, `test` or `[[ ]]`, and shellcheck-style reasoning. The lab should produce a script that gathers system information into a report without changing system configuration, then briefly schedule and remove one harmless test entry in a disposable VM.

### Lesson 8 — Text Processing and Pipelines

Show how Linux tools compose through standard input and output. Introduce redirection, `grep`, `sort`, `uniq`, `cut`, `tr`, `wc`, and `sed` or `awk` at an introductory level. Students should transform command output into a readable report and learn to verify intermediate results rather than writing opaque pipelines.

### Lesson 9 — Storage, Filesystems, and Backups

Return to the installation-safety theme with a deeper treatment of partitions, filesystems, mount points, removable media, and backup strategies. Use read-only inspection commands such as `lsblk`, `blkid`, `df`, `du`, and `mount`; demonstrate `tar` for an archive backup inside the lab VM. Do not require partitioning or filesystem formatting on personal hardware.

### Lesson 10 — Networking Fundamentals

Explain interfaces, addresses, routes, DNS, ports, and basic client/server behavior. Use `ip`, `ping`, `ss`, `resolvectl` or `getent hosts`, and `curl` against an approved, safe endpoint. The lab should compare local connectivity, name resolution, and application-layer access while distinguishing a network failure from a service failure.

### Lesson 11 — Services and System Administration

Introduce `systemd` concepts, units, service state, startup, dependencies, and logs. Use `systemctl` and `journalctl` on a harmless service or a preconfigured lab application. Students should practice status inspection, restarting an approved service, and reading recent logs, with explicit boundaries around privileged actions.

### Lesson 12 — Security and Safe Administration

Consolidate security habits: least privilege, authentication, authorization, updates, software provenance, backups, and cautious use of `sudo`. Cover SSH concepts and firewall principles without exposing a classroom machine unnecessarily. The lab should include an audit checklist and a decision exercise in which students identify the safest response to risky command examples.

### Lesson 13 — Troubleshooting and Documentation

Teach a method: define the symptom, establish what changed, collect evidence, form a hypothesis, test one variable, and document the result. Reuse `journalctl`, `systemctl`, `ip`, `df`, `du`, `ps`, and text-processing pipelines. The lab should provide a small set of seeded faults or misleading symptoms and assess the quality of the student's written diagnosis.

### Lesson 14 — Integration Capstone

Students combine the course skills in a bounded project, such as a documented local service, a system-information reporting tool, or a backup-and-monitoring workflow. The deliverables should include a working artifact, a setup script or procedure, a security and safety note, verification evidence, and a short troubleshooting record. The capstone should reward reliable reasoning and documentation rather than complexity for its own sake.

## Standards for every future lesson

- Keep Ubuntu Desktop LTS as the default environment unless a lesson explicitly needs a server or disposable VM.
- Introduce commands in the textbook before asking students to use them in the lab or assessment.
- Explain purpose, expected output, permissions, and safety implications for every command.
- Keep destructive operations inside disposable directories, snapshots, or instructor-provided virtual machines.
- Include at least 20 Moodle XML questions with both conceptual and command-purpose coverage.
- End the textbook chapter with its glossary, then provide instructor materials.
- Reuse terminology consistently: kernel, user space, shell, process, filesystem, distribution, package, service, and permission.
- Prefer evidence-based troubleshooting and written verification over memorizing exact output.

## Recommended next artifact

Develop Lesson 9 — Storage, Filesystems, and Backups — using the established lesson structure. Build on earlier filesystem navigation while keeping storage inspection read-only and backup exercises inside a practice directory or disposable VM.
