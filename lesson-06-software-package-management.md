# Lesson 6 — Software and Package Management

## Lesson purpose

Linux distributions deliver most system software as packages from managed repositories. Package tools keep track of installed files, dependencies, versions, and updates. This lesson deepens the APT introduction from Lesson 1 and shows how to find, inspect, install, update, and remove software while reviewing the proposed changes before confirming them.

## Learning objectives

By the end of the lesson, students will be able to:

- explain the role of a software package, repository, package index, and dependency;
- distinguish refreshing package metadata from upgrading installed software;
- search for and inspect a package with APT;
- install and remove a small package in a disposable Ubuntu VM;
- use `dpkg` to inspect package status, package files, and which package owns a path;
- compare repository types and Debian/Ubuntu, Fedora/RHEL, SUSE, and Arch package-management families;
- explain how packages provide shared libraries used by applications; and
- explain why package provenance, transaction review, and rollback planning matter.

## Instructor preparation

Use a disposable Ubuntu Desktop LTS VM with a working network connection and a current snapshot. Confirm that `apt`, `dpkg`, and the `tree` package are available from the configured Ubuntu repositories. If `tree` is already installed, use a different approved package known to be absent from the lab image. Never ask students to install or remove software on a personal or shared classroom host.

## Suggested lesson sequence

| Segment | Time |
|---|---:|
| Review and objectives | 10 min |
| Textbook and demonstrations | 45 min |
| Student lab | 45 min |
| Knowledge check and reflection | 15 min |

# Textbook chapter

## 1. Packages, repositories, and dependencies

A software package is a structured archive containing program files, metadata, and often scripts that help the system install, configure, or remove the software. Package metadata describes such things as the package name, version, architecture, description, dependencies, and source repository.

A repository is a managed source of packages and metadata. On Ubuntu, APT obtains packages from the configured Ubuntu repositories and other sources the administrator has deliberately enabled. Repository signing and package checks help the system verify that downloaded metadata and packages come from trusted sources. Adding an unfamiliar repository changes the software sources the system trusts, so it should not be treated as a casual fix for a missing package.

Repositories may be maintained by a distribution, a community, or a third party. Ubuntu's official repositories are maintained for Ubuntu releases. A Personal Package Archive (PPA) is an Ubuntu-related repository hosted on Launchpad by an individual or team; it is not the same as an official Ubuntu archive. Community sources such as Arch's AUR and independent vendor repositories can provide more software, but users should evaluate who maintains the software and how updates are delivered. APT repository definitions are stored in system configuration files; their exact layout can vary by Ubuntu release. This lesson inspects repository origins with `apt policy` but does not add or edit sources.

A dependency is another package needed for a program to work. A package manager calculates dependencies and presents a proposed transaction so the user can see what will be installed, upgraded, or removed. Reviewing this summary is an important safety step, especially when a transaction affects many packages.

## 2. APT and the Debian package database

Ubuntu uses Debian-format `.deb` packages. APT provides a higher-level interface for working with repository metadata and resolving dependencies. `dpkg` maintains the local package database and performs lower-level package operations. APT uses `dpkg` to install or remove package files after it has resolved what is needed.

Use APT for ordinary installation, upgrade, and removal because it can resolve dependencies using configured repositories. Use `dpkg` for inspection tasks in this lesson. Installing a local `.deb` directly with `dpkg` does not automatically obtain missing dependencies; use the repository-aware package manager for routine system changes.

## 3. Refreshing metadata is different from upgrading software

`sudo apt update` downloads current package indexes from configured repositories. It refreshes the local information about which package versions are available; it does not itself upgrade installed applications.

`sudo apt upgrade` uses that information to upgrade installed packages when compatible updates are available. The command may propose several package changes. Review the summary before confirming. A whole-system upgrade can affect important software and should be performed in a planned maintenance window with a current backup or VM snapshot.

The order matters: refresh the package index before deciding what updates are available. An update is not the same as upgrading to a new Ubuntu release; release upgrades have a separate procedure and are outside this lesson's lab.

APT also offers `sudo apt full-upgrade`, which may install or remove packages when required to complete a set of upgrades. Because the transaction can remove packages, it requires careful review and is not used in the student lab. The older name `dist-upgrade` may appear in documentation and older command examples.

## 4. Find and evaluate a package

`apt search keyword` searches package names and descriptions in the local package indexes. Search results are candidates, not endorsements. Confirm that the package is from an expected repository and matches the task.

`apt show package-name` displays details such as the package description, version, architecture, size, and dependencies. `apt policy package-name` shows installed and candidate versions and the repository origins that provide them. These inspection steps help answer “what is this package?” before installing it.

Package names and available versions vary by Ubuntu release and enabled repository. If a search returns no result, check the spelling and enabled sources rather than downloading a random installer from a search result.

## 5. Install and remove with APT

`sudo apt install package-name` asks APT to install a package and its required dependencies. Before confirming, read the transaction summary. If it proposes removing unexpected packages or changing a large part of the system, cancel and investigate.

`sudo apt remove package-name` removes the package's program files while generally retaining system-wide configuration files that may be needed if the package is reinstalled. Removal can affect dependent programs; APT reports proposed changes before asking for confirmation. Do not approve a removal list you do not understand. The course lab does not use automatic cleanup commands because they may remove packages beyond the one being studied.

`sudo apt purge package-name` also asks APT to remove the package's system-wide configuration files. It does not promise to erase user data stored elsewhere, and it should not be used casually. The student lab uses `remove` so learners can see the distinction without purging configuration.

Administrative commands use `sudo` because package installation changes protected system locations and the package database. A user should enter their own password when authorized, inspect the proposed transaction, and avoid adding `-y` to practice commands so there is an opportunity to review.

## 6. Inspect installed packages with `dpkg`

`dpkg -s package-name` reports the local status and metadata for a named package. It is useful for checking whether a package is installed and reading its version and description.

`dpkg -L package-name` lists paths recorded as belonging to that installed package. It does not mean every listed file should be edited or removed manually.

`dpkg -S /path/to/file` searches the package database for a package that owns the specified path. Some generated files may not be recorded as belonging to a package, so a “no path found” result does not always mean the file is unrelated to installed software.

These `dpkg` commands are for inspection in this lesson. Do not delete package-owned files by hand; use the package manager so its database and dependencies remain consistent.

## 7. Different distributions use different package systems

The package-management concepts transfer across distributions, but the tools and package formats differ:

| Distribution family | Common package format | Higher-level package tool | Lower-level package tool |
|---|---|---|---|
| Debian and Ubuntu | `.deb` | APT (`apt`) | `dpkg` |
| Fedora and many RHEL-based systems | `.rpm` | DNF (`dnf`) | RPM (`rpm`) |
| SUSE and openSUSE | `.rpm` | Zypper (`zypper`) | RPM (`rpm`) |
| Arch Linux | `.pkg.tar.*` | Pacman (`pacman`) | Pacman also performs the package operations |

The tools all work with package sources and system state, but their command syntax, repository policies, and upgrade models are not interchangeable. YUM is an older package-management tool that DNF replaced in current Fedora and many RHEL-based workflows. Arch Linux uses a rolling release model, so its users apply updates within that model and follow distribution notices. Students should use the tools documented for the system they are operating rather than copying an APT command into another distribution.

## 8. Shared libraries are packages too

Many programs rely on shared libraries: compiled code that multiple programs can use at runtime. These libraries are often packaged separately, so APT may install them as dependencies. A shared-library filename commonly includes `.so` followed by a version number, such as `libm.so.6`. Updating a library package can provide a fix to multiple programs that use it.

The `ldd` command displays the shared libraries required by an executable. For example, `ldd /usr/bin/tree` can show dependencies of the trusted, package-managed program installed in this lesson. Use `ldd` only on known executables from trusted sources; do not use it to inspect an arbitrary downloaded binary. If a program reports a missing library, consult the distribution's package database rather than downloading a standalone library file.

## 9. Recovery, rollback, and trustworthy sources

Removing a package does not always restore every setting or user-data file to its earlier state. An upgrade may replace program files while preserving local configuration. A package operation may also change dependencies. A VM snapshot or tested backup provides a dependable way to return to a known state; do not assume that package caches provide a complete rollback plan.

Before a large upgrade, confirm that important data is backed up, review what APT proposes, and have a recovery path. In a class VM, restore the snapshot if the lab leaves the package state unsuitable for the next group.

Avoid installing software by piping an arbitrary download directly into a shell or running an unexplained installer script. Such scripts can change the system with the user's privileges, may bypass package tracking, and can be difficult to audit or remove. Prefer signed packages from the distribution's configured repositories or a software source whose publisher and instructions you have verified.

# Guided demonstrations

## Demonstration 1: Refresh indexes and search

In the disposable VM:

```bash
sudo apt update
apt search tree
```

Explain that the first command refreshes package indexes and the second searches those local indexes. It does not install a package.

## Demonstration 2: Inspect the package before installation

```bash
apt show tree
apt policy tree
```

Identify the description, candidate version, and repository origin. If the candidate comes from an unexpected source, stop before installing.

## Demonstration 3: Review and install a small package

```bash
sudo apt install tree
dpkg -s tree
dpkg -L tree
dpkg -S /usr/bin/tree
```

At APT's confirmation prompt, pause and read the package and dependency summary before proceeding. The `dpkg` commands inspect the package state and files.

## Demonstration 4: Remove the lab package

```bash
sudo apt remove tree
dpkg -s tree
```

Review the removal summary before confirming. Explain that the package's configuration may remain. Restore the VM snapshot after the demonstration if a clean starting state is needed.

## Demonstration 5: Compare update and upgrade

In an instructor-prepared VM, run `sudo apt update`, then begin `sudo apt upgrade` only when there is a current snapshot and the instructor can review the proposed changes. Students should distinguish refreshed indexes from packages that will actually be upgraded. Cancel if the transaction is outside the demonstration plan.

## Demonstration 6: Inspect shared-library dependencies

After installing the trusted `tree` package, run:

```bash
ldd /usr/bin/tree
```

Identify a shared-library path in the output. Explain that `ldd` is for trusted executables in this course, not arbitrary downloaded binaries. The command inspects dependencies; it does not install or update packages.

# Student lab: Lesson 6 — Software and Package Management

## Safety and scope

Use only the disposable course VM. Confirm that a current snapshot exists before installing or removing a package. Do not use `-y`, do not add a repository, do not download an installer script, and do not approve a transaction that proposes unexpected removals or a large unrelated change. If the expected small package is already installed, ask the instructor for the approved alternative.

## Part A: Refresh package indexes and search

```bash
sudo apt update
apt search tree
```

Record what `apt update` refreshes and identify the package description shown in the search results.

## Part B: Inspect the candidate package

```bash
apt show tree
apt policy tree
```

Record the candidate version and repository origin. Explain why checking the source is part of deciding whether to install a package.

## Part C: Install and inspect

```bash
sudo apt install tree
dpkg -s tree
dpkg -L tree
dpkg -S /usr/bin/tree
```

Before confirming, read the transaction summary and verify that it installs the intended package and any expected dependencies. Use the `dpkg` output to record the package status, one installed path, and the package owning `/usr/bin/tree`.

## Part D: Remove the lab package

```bash
sudo apt remove tree
```

Review the proposed removal and confirm only if the transaction matches the lab. Explain why removing a package through APT is safer than deleting its files by hand. Leave the VM ready for snapshot restoration or instructor review.

## Part E: Inspect a shared-library dependency

If the instructor leaves the `tree` package installed for this step, inspect its dependencies:

```bash
ldd /usr/bin/tree
```

Name one shared library in the output. Do not use `ldd` on files obtained from unknown sources.

## Part F: Plan a package update

Do not upgrade the entire VM as part of the student lab. Instead, describe the safe sequence for planned maintenance: ensure a backup or snapshot exists, refresh the package index, inspect proposed package changes, confirm only an understood transaction, then verify the system.

## Lab reflection

1. What does `apt update` change, and what does it leave unchanged?
2. Why inspect `apt policy` before installing a package?
3. What does APT do with dependencies during installation?
4. Why should students review the transaction before confirming it?
5. Why is deleting a package's files manually a poor removal method?
6. How does a PPA differ from an Ubuntu official repository?
7. Why should repository origins be checked before installation?
8. What shared library did `ldd` show for the trusted lab executable?

## Lab success criteria

- The student distinguishes package-index refresh from package upgrade.
- The student uses `apt search`, `apt show`, and `apt policy` to evaluate a package.
- The student installs and removes only the approved lab package in the disposable VM.
- The student uses `dpkg` to inspect package status, file paths, and path ownership.
- The student can state a recovery step and identify why an unexplained installer is risky.

# Knowledge check

1. What is a software package?
2. What is a repository?
3. What is a package dependency?
4. What does `sudo apt update` refresh?
5. Does `apt update` itself upgrade installed applications?
6. What does `sudo apt upgrade` do?
7. What does `apt search tree` search?
8. What information can `apt show tree` provide?
9. What does `apt policy tree` help you inspect?
10. What does `sudo apt install tree` ask APT to do?
11. Why review APT's proposed transaction before confirming?
12. What does `sudo apt remove tree` generally remove, and what may remain?
13. What does `dpkg -s tree` report?
14. What does `dpkg -L tree` list?
15. What does `dpkg -S /usr/bin/tree` search for?
16. Why is APT preferred over direct `dpkg` installation for ordinary repository software?
17. Which package-management family uses DNF and RPM?
18. Which package tool is associated with Arch Linux?
19. Why should students avoid running an unexplained installer script?
20. What is a dependable recovery method before a large package upgrade?
21. How does `apt purge` differ from `apt remove`?
22. What should you check before adding a PPA or third-party repository?
23. What does `apt full-upgrade` potentially do that `apt upgrade` normally avoids?
24. What does `ldd /usr/bin/tree` display?
25. Which tool is used by SUSE/openSUSE, and which is the older predecessor to DNF?

## Answer key

1. A structured archive of software files and metadata used by a package system.
2. A managed source from which package metadata and packages are obtained.
3. Another package needed for software to work correctly.
4. The local package indexes from configured repositories.
5. No. It refreshes package metadata; installed software is upgraded separately.
6. Upgrades installed packages when compatible updates are available, after the user reviews and confirms the proposed changes.
7. Package names and descriptions in the local package indexes.
8. Description, version, architecture, size, and dependency details.
9. Installed and candidate versions and their repository origins.
10. Install the named package and resolve its dependencies from configured sources.
11. The transaction may affect dependencies or propose removals beyond the package named.
12. It removes the package's program files; system-wide configuration files may remain.
13. Local status and metadata for the package.
14. Paths recorded as belonging to the package.
15. Which installed package, if any, owns that path according to the package database.
16. APT resolves dependencies and obtains packages from configured repositories; `dpkg` alone does not automatically fetch dependencies.
17. Fedora and many RHEL-based distributions.
18. Pacman.
19. It can make unreviewed system changes, bypass package tracking, and be difficult to audit or remove.
20. A current VM snapshot or a tested backup.
21. `purge` also removes system-wide package configuration files; `remove` generally leaves them.
22. Who maintains it, whether the publisher is trusted, and whether it is intended for the current Ubuntu release.
23. It may install or remove packages to complete the upgrade transaction.
24. The shared libraries required by that executable.
25. Zypper; YUM.

## Exit ticket

In one sentence, explain the difference between refreshing package metadata and upgrading installed software.

# Chapter glossary

**APT:** The higher-level package-management tool used by Debian and Ubuntu systems.  
**Dependency:** A package required by another package or program.  
**Package:** A structured archive containing software files and package metadata.  
**Package index:** Local metadata describing packages available from configured repositories.  
**Package manager:** A tool that installs, updates, tracks, and removes packages.  
**Personal Package Archive (PPA):** A user- or team-maintained Ubuntu-related repository hosted on Launchpad.  
**Repository:** A managed source of package files and package metadata.  
**Shared library:** Compiled code that multiple programs can use at runtime.  
**Transaction:** The proposed set of package changes, including installs, upgrades, and removals.  
**Upgrade:** Replacing an installed package version with a newer available version.  

# Instructor materials

## Instructor notes

- Keep installation and removal practice inside a disposable VM with a current snapshot.
- Emphasize the separation between index refresh (`apt update`) and software upgrade (`apt upgrade`).
- Have learners read the proposed transaction before confirming any package change.
- Never add `-y` to student commands; the confirmation prompt is part of the safety instruction.
- If the package is already installed, choose an instructor-approved substitute rather than removing existing software without a plan.
- Do not ask students to automatically clean up orphaned packages, purge package configuration, manually install a local package archive, or run arbitrary installer scripts in this lab.
- Distinguish Ubuntu official repositories from PPAs, community sources, and third-party repositories; do not add a repository during the student lab.
- Treat `full-upgrade` and `purge` as recognition and review topics only; the student lab does not run either operation.
- Demonstrate `ldd` only on the trusted package-managed `/usr/bin/tree` executable. Do not use it on downloaded or otherwise untrusted binaries.
- Explain that Ubuntu/Debian, Fedora/RHEL, SUSE, and Arch differ in package tools and update models; examples from one family are not drop-in replacements for another.
- Restore the class VM snapshot between groups or after the lesson if needed.

## Suggested slide outline

1. Packages, repositories, and dependencies
2. APT and `dpkg`
3. Package index refresh versus software upgrade
4. Search and inspect before installing
5. Review the transaction summary
6. Install and remove through APT
7. Inspect package status and files with `dpkg`
8. Debian/Ubuntu, Fedora/RHEL, SUSE, and Arch package families
9. Shared libraries and package dependencies
10. Trustworthy sources, backups, and recovery

## Commands introduced

`apt search`, `apt show`, `apt policy`, `apt install`, `apt remove`, `apt purge`, `apt full-upgrade`, `dpkg -s`, `dpkg -L`, `dpkg -S`, and `ldd`. `apt update` and `apt upgrade` are reused from Lesson 1 and deepened here. `dnf`, `rpm`, `zypper`, `pacman`, and legacy `yum` are introduced for cross-distribution recognition. `tree` is used as an example program installed from a repository.
