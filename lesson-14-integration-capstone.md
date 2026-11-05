# Lesson 14 — Integration Capstone

## Lesson purpose

The capstone brings the course together in a small, reviewable project. Students create a Bash workflow that gathers a limited system report, archives instructor-created practice data, checks the archive contents, and demonstrates a restore. They apply least privilege, safe file handling, evidence-based troubleshooting, and clear documentation. The project runs in a personal course directory and makes no system-wide changes.

## Learning objectives

By the end of the capstone, students will be able to:

- plan a small Linux task by connecting relevant course concepts and tools;
- write and run a readable Bash script in a bounded practice directory;
- gather system information without changing system configuration;
- create, inspect, restore, and compare a backup of synthetic practice data;
- explain the permissions, privacy, and trust boundaries of the project;
- verify the result against stated acceptance criteria; and
- submit a concise setup guide, evidence, and troubleshooting record.

## Instructor preparation

Use an Ubuntu Desktop LTS VM or student-owned course VM with Bash, GNU `tar`, and the basic utilities used in Lessons 1–13. Students need a writable home directory and a text editor. No administrator privileges, network services, external repositories, cron entries, or system configuration changes are needed. Encourage snapshots only for disposable VMs; the project itself should be safe without one. Provide the synthetic starter data shown in the lab or have students create it themselves. Have an instructor-generated example of a completed report and archive listing available, with identifying system details removed.

## Suggested lesson sequence

| Segment | Time |
|---|---:|
| Project brief, scope, and acceptance criteria | 20 min |
| Build the report and archive workflow | 60 min |
| Restore, verify, and troubleshoot | 45 min |
| Peer review, documentation, and reflection | 35 min |

# Textbook chapter

## 1. A capstone is an integration exercise

An integration project uses skills together to complete a bounded goal. A good beginner project is small enough to understand, but complete enough to require planning, implementation, verification, and documentation. This capstone uses ideas from several lessons:

| Course area | How the project uses it |
|---|---|
| Command line and paths | Keep all project files under one known directory |
| Files, permissions, and text tools | Create a script and readable report safely |
| Shell scripting | Repeat the report and archive steps consistently |
| Storage and backups | Archive synthetic data and restore a copy |
| Networking and services | Understand which system details a report might reveal; no network service is started |
| Security | Use least privilege and avoid collecting secrets or personal data |
| Troubleshooting | Compare expected results with observed output and record evidence |

The deliverable is a useful workflow, not a production backup system. It does not encrypt data, copy it to a separate device, schedule itself, or manage a live service. Those would require additional design and operational controls.

## 2. Define scope before writing commands

Write the project boundary first. This project uses `~/linux-capstone/` and only instructor-created, non-sensitive files in its `demo-data/` subdirectory. The script writes a report and compressed archive into subdirectories under that project. It must not read `/etc/shadow`, collect passwords or tokens, sweep the whole home directory, change system configuration, install packages, alter firewall or SSH settings, or stop processes.

A clear boundary protects both the user and the result. A path such as `~/linux-capstone/demo-data` communicates what will be included. A vague target such as “back up my files” does not specify which files, whether they are safe to copy, or where the result will go. Always inspect a script and understand its target paths before running it.

## 3. Plan the data flow

The project has four kinds of output:

```text
synthetic practice files ──tar──> compressed archive
             │                         │
             │                         └──list and restore──> restore-check copy
             │
current system facts ──script──> timestamped text report
```

The archive contains only the synthetic practice directory. The report contains a small set of system facts useful for basic context: the time it was generated, kernel/release summary, uptime, memory summary, and free space for the user's home filesystem. Even basic system details can reveal information about a device, so reports should be reviewed and redacted before sharing.

## 4. Build a readable script

Lesson 7 introduced Bash scripts, quoted variables, redirection, and exit status. The following example shows the intended size and structure. Review it line by line before running it:

```bash
#!/usr/bin/env bash

project_dir="$HOME/linux-capstone"
report_dir="$project_dir/reports"
backup_dir="$project_dir/backups"

mkdir -p "$report_dir" "$backup_dir"

timestamp="$(date +%Y%m%d-%H%M%S)"
report="$report_dir/system-report-$timestamp.txt"
archive="$backup_dir/demo-data-$timestamp.tar.gz"
archive_list="$backup_dir/archive-list-$timestamp.txt"

{
    printf 'Linux Course Capstone System Report\n'
    printf 'Generated: '
    date --iso-8601=seconds
    printf '\nKernel and release:\n'
    uname -srm
    printf '\nUptime and load:\n'
    uptime
    printf '\nMemory summary:\n'
    free -h
    printf '\nHome filesystem usage:\n'
    df -h "$HOME"
} > "$report"

if tar -czf "$archive" -C "$project_dir" demo-data; then
    if tar -tzf "$archive" > "$archive_list"; then
        printf 'Report: %s\n' "$report"
        printf 'Archive: %s\n' "$archive"
        printf 'Archive listing: %s\n' "$archive_list"
    else
        printf 'Archive was created, but listing verification failed.\n' >&2
        exit 1
    fi
else
    printf 'Archive creation failed.\n' >&2
    exit 1
fi
```

The script uses quoted paths so spaces remain part of a single argument. `mkdir -p` creates the report and backup directories if needed and does not fail merely because they already exist. A timestamp gives each run a distinct output name. The braces group report commands so one redirection writes their combined output to the report file. The `if` statements check the exit status of archive creation and archive listing; they do not prove that the archived files are identical to the originals. A separate restore-and-compare step will provide stronger verification.

`tar -C "$project_dir" demo-data` changes the directory used as the archive base and includes only the named synthetic-data directory. This keeps the archive paths tidy and avoids accidentally including the whole project or home directory. Do not replace `demo-data` with `/`, `~`, or an unreviewed broad path.

## 5. Test and run within the project boundary

Before execution, inspect the script and confirm the values of `project_dir`, `demo_dir`, `report_dir`, and `backup_dir`. Syntax-check it with `bash -n` and then run it with Bash:

```bash
bash -n "$HOME/linux-capstone/capstone.sh"
bash "$HOME/linux-capstone/capstone.sh"
```

`bash -n` asks Bash to parse the script without executing it. A successful syntax check does not prove that the script's logic is correct, that its paths are appropriate, or that its output is accurate. Running it with `bash` does not require changing its executable bit. If students choose to launch it directly, they may use `chmod u+x` on the script in the project directory and then execute that specific file. Do not use `sudo`.

The script is intended to read selected system state and write report, archive, and listing files inside the project directory. It does not configure the system. It still creates files and consumes storage; the student should inspect the output and remove only their own project artifacts when the instructor says the work is complete.

## 6. Verify the archive with a restore

An archive listing confirms that the archive contains named entries; it does not confirm that extraction succeeds or that the restored content matches the source. Use an empty, project-local restore directory and compare it with the source:

```bash
mkdir -p "$HOME/linux-capstone/restore-check"
tar -xzf "$HOME/linux-capstone/backups/ARCHIVE_NAME.tar.gz" \
    -C "$HOME/linux-capstone/restore-check"
diff -r "$HOME/linux-capstone/demo-data" \
    "$HOME/linux-capstone/restore-check/demo-data"
```

Replace `ARCHIVE_NAME` with the archive path printed by the script. `tar -xzf` extracts the gzip-compressed archive into the specified restore directory. Extraction writes files, so use only the approved project-local directory and a known archive. `diff -r` compares directory trees recursively; no output and a successful exit status indicate that the compared contents match. A difference should be investigated rather than ignored. Do not extract an unknown archive into a real home directory or system path.

The restore check verifies a small local archive but not a complete backup strategy. A real backup plan needs a separate failure domain, appropriate access control, retention, and periodic restore tests. A second directory on the same disk is convenient for this exercise but would not protect against disk failure.

## 7. Verify the report and explain its limits

Open the generated report with a text viewer such as `less` or `cat`, introduced earlier. Check that its timestamp, system summary, memory summary, and home filesystem line are present and readable. Compare those sections with direct command output if needed. Command output may vary by distribution and system state.

The report is a snapshot. It does not diagnose a problem, prove the machine is healthy, or capture every relevant fact. It omits logs, network addresses, process lists, credentials, and personal files by design. If an instructor-approved project needs another field, students must state why it is useful, whether it is sensitive, and how it will be protected before adding it.

## 8. Use troubleshooting evidence

If the script fails, capture the exact command, error message, and time. Check the narrowest likely cause: is the script path correct, is the project directory writable, does `demo-data/` exist, and does the archive command return success? Compare the observed result with the expected behavior. Change one thing at a time and rerun only after understanding the effect. Do not solve a permissions error by using `sudo` or making a directory world-writable.

The final record should distinguish observation from explanation. For example, “`tar` reported that `demo-data` could not be found” is an observation. “The project path variable is wrong” is a hypothesis; inspect the path and test it before recording that as the cause.

## 9. Acceptance criteria

The capstone is complete when:

- the script is readable and remains inside the `linux-capstone` project boundary;
- the report contains the selected system summary and a timestamp;
- the archive contains only the synthetic `demo-data/` content;
- the archive can be listed and extracted into a project-local restore directory;
- the restored directory matches the source according to `diff -r` or an equivalent documented comparison;
- no system-wide changes, elevated privileges, or non-approved data were used; and
- the setup guide, security note, verification evidence, and troubleshooting record are complete.

# Guided demonstrations

## Demonstration 1: Trace each path

Walk through the example script without running it. Ask students to point out the project boundary, each output path, the one archive input directory, and the commands that only read system state. Discuss why quoting each path matters and what would change if `tar` included `$HOME` instead of `demo-data`.

## Demonstration 2: Syntax check versus successful behavior

Show `bash -n` on instructor-provided script text. Explain that a parse success means only that Bash accepts the syntax. Then inspect a sample run's exit status and artifacts. Do not use a deliberately dangerous script; use a harmless missing-directory or misspelled-variable scenario in the disposable instructor VM.

## Demonstration 3: Restore and compare

Using synthetic data in the instructor VM, show the archive listing, extract to an empty restore-check directory, and compare source with restored copy using `diff -r`. Explain that a second copy on the same disk is a verification exercise, not an independent backup.

# Student lab — Build, restore, and document

## Safety boundaries

Work only under `~/linux-capstone/` in an approved VM or local practice environment. Use invented sample text, not personal documents or course records. Do not use `sudo`, schedule the script, install software, change system settings, start a network listener, or archive `/`, the whole home directory, or removable media. Review paths before running commands that create, archive, or extract files.

## Part A: Create the project and synthetic input

Create a small project tree and a sample file:

```bash
mkdir -p "$HOME/linux-capstone/demo-data"
printf 'Synthetic course practice data.\n' > \
    "$HOME/linux-capstone/demo-data/notes.txt"
```

Create `~/linux-capstone/capstone.sh` in a text editor and enter the example script from the textbook. Read it carefully. Confirm that it includes only `demo-data/` as archive input and writes all outputs under the project directory.

## Part B: Syntax-check and run

Run:

```bash
bash -n "$HOME/linux-capstone/capstone.sh"
bash "$HOME/linux-capstone/capstone.sh"
```

If the script reports a syntax error or cannot write an output, record the message and inspect the relevant lines and paths. Do not add `sudo`. After it succeeds, record the report path, archive path, archive-list path, and the script's exit status.

## Part C: Review and verify outputs

Open the report and archive listing. Confirm the report has the required sections and that the listing contains the `demo-data/` path and its sample file. Create an empty restore directory and extract the archive there:

```bash
mkdir -p "$HOME/linux-capstone/restore-check"
tar -xzf "$HOME/linux-capstone/backups/ARCHIVE_NAME.tar.gz" \
    -C "$HOME/linux-capstone/restore-check"
diff -r "$HOME/linux-capstone/demo-data" \
    "$HOME/linux-capstone/restore-check/demo-data"
```

Replace the archive placeholder with the name printed by the script. Record whether `diff -r` produced output. If there are differences, identify the paths and investigate without overwriting the source.

## Part D: Security and troubleshooting review

Write a short note answering:

1. Which directory does the script archive, and how did you verify that boundary?
2. What information in the report might be sensitive if shared publicly?
3. Why does a successful archive listing not replace a restore test?
4. What would you check first if the script says permission denied?
5. Which changes would require an instructor or administrator's approval?

Then make a troubleshooting record for one issue you encountered, or for a hypothetical issue supplied by the instructor. Include the symptom, relevant evidence, hypothesis, test, result, and next step. Do not claim a root cause unless the test supports it.

## Deliverables

Submit:

- `capstone.sh`;
- one redacted generated report and its archive listing;
- a restore verification result (for example, a note that `diff -r` returned no differences);
- a short setup/run guide with required paths and commands;
- the security note and troubleshooting record; and
- a brief reflection describing one limitation and one possible future improvement.

Do not submit the archive if it contains anything beyond synthetic practice data. Remove hostnames, usernames, addresses, and other identifying details from screenshots or output included in the submission.

# Knowledge check and answer key

1. **What makes a capstone project appropriately bounded?** It has a clear purpose, known inputs and outputs, an explicit directory/safety boundary, and acceptance criteria.
2. **Why does the project use synthetic practice data?** It avoids exposing or accidentally archiving personal or sensitive information.
3. **What is the purpose of `mkdir -p "$report_dir" "$backup_dir"` in the script?** Create the required output directories, including when they already exist.
4. **Why quote path variables in Bash?** Quoting preserves spaces and prevents unintended word splitting and wildcard expansion.
5. **What does `date +%Y%m%d-%H%M%S` provide in the script?** A timestamp suitable for distinguishing output filenames from separate runs.
6. **What does `date --iso-8601=seconds` provide in the report?** A readable, standardized timestamp with seconds precision.
7. **What does `uname -srm` summarize?** Kernel name, release, and machine hardware name/architecture information.
8. **What does `uptime` contribute to the report?** System uptime and load averages at the time of collection.
9. **What does `free -h` summarize?** Memory and swap information in human-readable units.
10. **What does `df -h "$HOME"` report?** Human-readable capacity/usage for the filesystem containing the user's home path.
11. **Why is the script's report only a snapshot?** It records selected information at one time and does not prove overall health or explain every later problem.
12. **Why does the example use `tar -C "$project_dir" demo-data`?** It archives only the named synthetic directory using a project-relative path.
13. **What does `tar -czf ARCHIVE -C DIR INPUT` do?** Creates a gzip-compressed tar archive from the named input relative to the selected directory.
14. **What does `tar -tzf ARCHIVE` do?** Lists entries in a gzip-compressed tar archive without extracting them.
15. **What does `tar -xzf ARCHIVE -C DESTINATION` do?** Extracts a gzip-compressed tar archive into the specified destination directory.
16. **Why extract only into a known project-local directory?** Extraction writes files; a bounded destination reduces the risk of overwriting unrelated data.
17. **What does `diff -r SOURCE RESTORE` check?** Recursively compares the contents of the two directory trees.
18. **What does no output from a successful `diff -r` usually indicate?** No content differences were found between the compared directory trees.
19. **Does a successful `tar -tzf` prove a backup can be restored?** No. It lists archive entries; an extraction and comparison test gives stronger evidence.
20. **Why is a second directory on the same disk not an independent backup?** A disk failure or other shared event can affect both copies.
21. **What does `bash -n SCRIPT` do?** Parses the Bash script for syntax without executing its commands.
22. **Does a successful syntax check prove the script is safe and logically correct?** No. Review paths, logic, permissions, and output, then test in the bounded lab.
23. **Why should students not fix a permission error by adding `sudo`?** It expands authority and may hide an incorrect path or ownership assumption; the cause should be inspected first.
24. **What should a useful troubleshooting record distinguish?** Direct observations from hypotheses and conclusions.
25. **Why redact a generated system report before sharing?** It may disclose details about the device, operating system, storage, or user environment.
26. **What is a suitable improvement for a future version of this project?** Examples include stronger error handling, a restore test built into a separate safe mode, a retention plan, or a separately stored backup, with appropriate review.

# Chapter glossary

- **Acceptance criteria:** Observable conditions used to decide whether a project meets its requirements.
- **Archive:** A file that combines selected files and directory structure, optionally compressed.
- **Capstone:** A culminating project that integrates knowledge and skills from a course.
- **Data boundary:** A stated limit on what a project is allowed to read, change, or include.
- **Exit status:** A numeric result returned by a command or script; zero conventionally indicates success.
- **Integration:** Combining multiple concepts or tools to complete one coherent task.
- **Restore test:** Extracting or recovering a backup and checking that the recovered data is usable.
- **Synthetic data:** Invented practice data that does not contain real personal or sensitive information.
- **Verification:** A check that compares the result with expected behavior or acceptance criteria.

# Instructor materials

## Facilitation notes

- Assess the process and evidence, not only whether the script ran successfully on the first attempt.
- Ask students to trace every read path and write path. The archive must contain only the designated synthetic directory.
- Do not require students to upload host-specific reports or archives to a public repository.
- Accept equivalent editors and minor report formatting differences if the safety boundary and required content remain clear.
- Treat a sensible limitation statement as evidence of understanding; the workflow is not a production backup product.

## Assessment rubric (20 points)

| Criterion | Points |
|---|---:|
| Script is readable, quoted, and stays within the project directory | 4 |
| Report contains selected system facts and a timestamp | 3 |
| Archive is limited to synthetic data and its contents are inspected | 3 |
| Restore is performed in a bounded location and compared with source | 4 |
| Setup/run guide and safety note are clear and accurate | 2 |
| Troubleshooting record separates evidence from inference | 2 |
| Submission is redacted and identifies limitations/future improvement | 2 |

## Course reflection prompts

- Which earlier lesson contributed the most useful tool to your project, and why?
- Which assumption did you need to verify rather than accept?
- What would need to change before this workflow could be used for important organizational data?

## References

- [GNU Bash manual](https://www.gnu.org/software/bash/manual/bash.html)
- [GNU tar manual](https://www.gnu.org/software/tar/manual/)
- [Ubuntu `diff` manual](https://manpages.ubuntu.com/manpages/noble/man1/diff.1.html)
