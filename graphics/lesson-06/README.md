# Lesson 6 graphics

[Course graphics index](../README.md) · [Source chapter](../../lesson-06-software-package-management.md)

Production: AI-generated illustrations using the built-in image_gen tool, continuing the course's navy, teal, and amber style. All figures are drafts for instructional review.

## Figure 6.1 — From repository to installed package

![Configured repositories supply package indexes and Debian package archives. On Ubuntu, APT uses local indexes to plan dependencies and download selected packages. After transaction review and confirmation, dpkg installs package files and records local package state.](01-repository-apt-dpkg.png)

- **Placement:** Section 2, after the description of APT and dpkg and before the routine-use guidance.
- **Learning purpose:** Distinguish repository metadata, local package indexes, dependency planning, installation, and the local package database.
- **Caption:** **Figure 6.1.** APT uses repository metadata to plan package operations and obtain the required archives. After the user reviews and confirms the proposed changes, it uses `dpkg` for local installation and package-state tracking. Available-package indexes and the local installed-package database serve different purposes.
- **Alt text:** Configured repositories supply package indexes and Debian package archives. On Ubuntu, APT uses local indexes to plan dependencies and download selected packages. After transaction review and confirmation, dpkg installs package files and records local package state.
- **Prompt:** [01-generation-prompt.txt](01-generation-prompt.txt).
- **Review note:** Conceptual relationships, not an exact chronological trace of an APT transaction. Normal interactive APT installation presents its confirmation before downloading selected package archives; downloads, verification, unpacking, configuration, and scripts are condensed here. Metadata refresh does not install or upgrade applications. The picture assumes the lesson's interactive commands without automatic confirmation.

## Figure 6.2 — apt update is not apt upgrade

![sudo apt update refreshes local package indexes from repositories while leaving installed software unchanged. sudo apt upgrade uses indexes and installed state to propose compatible updates, which are reviewed and confirmed before installed packages change. Neither command changes the Ubuntu release.](02-update-versus-upgrade.png)

- **Placement:** Section 3, after the paragraph distinguishing package updates from a new Ubuntu release and before full-upgrade recognition material.
- **Learning purpose:** Separate refreshing available-package information from changing installed package versions.
- **Caption:** **Figure 6.2.** `apt update` refreshes information about available packages. `apt upgrade` uses that information to propose and apply compatible updates to installed packages. Review changes before confirmation; an index refresh alone does not upgrade software or move Ubuntu to a new release.
- **Alt text:** sudo apt update refreshes local package indexes from repositories while leaving installed software unchanged. sudo apt upgrade uses indexes and installed state to propose compatible updates, which are reviewed and confirmed before installed packages change. Neither command changes the Ubuntu release.
- **Prompt:** [02-generation-prompt.txt](02-generation-prompt.txt).
- **Review note:** An upgrade may have nothing to do, may install new dependencies, or may hold back packages rather than remove installed ones. Whole-system upgrading is planned instructor maintenance in this lesson, not a student-lab step. A backup or snapshot is a recovery measure, not a guarantee that every restore procedure will work without testing.

## Figure 6.3 — Dependencies connect software packages

![Two conceptual application packages depend on one shared-library package; one application also needs another package. APT resolves the required set, and one library update can benefit several programs using it. Dependency arrows point from a package to what it requires.](03-package-dependencies.png)

- **Placement:** Section 8, after the introductory shared-library paragraph and before the trusted-executable ldd example.
- **Learning purpose:** Show shared dependencies and connect package planning to runtime library use.
- **Caption:** **Figure 6.3.** Different application packages may depend on the same shared-library package. APT resolves the required package set and presents proposed changes for review. Updating a shared library can benefit several programs, though running programs may need restarting to use the new code.
- **Alt text:** Two conceptual application packages depend on one shared-library package; one application also needs another package. APT resolves the required set, and one library update can benefit several programs using it. Dependency arrows point from a package to what it requires.
- **Prompt:** [03-generation-prompt.txt](03-generation-prompt.txt).
- **Review note:** Invented A/B package labels explain the relationship; they are not tree's actual dependency list. Libraries may be dependencies of other libraries, and not every package contains a shared library. Dependency handling does not remove the need to check origin, compatibility, transaction scope, and recovery plans.
