# Lesson 4 — Users, Groups, and Permissions

## Lesson purpose

Linux uses user and group identities to decide who may read, change, or run a file and who may enter a directory. This lesson connects the account records introduced in Lesson 3 with the permission information learners have already seen in `ls -l`. Students practice permission changes only on files in a course lab directory. Account creation and ownership changes that require administrator access are instructor demonstrations in a disposable, approved virtual machine.

## Learning objectives

By the end of the lesson, students will be able to:

- distinguish a username from a numeric user ID (UID), and a group name from a group ID (GID);
- identify a file's owner, group, and permission bits in a long listing;
- explain read, write, and execute permissions for files and directories;
- inspect their own identity and group memberships with `id` and `groups`;
- change permissions on lab files using symbolic and octal notation;
- recognize special permission bits and explain what ACLs add to the basic model;
- describe how ownership, groups, and `umask` affect access; and
- explain why account and ownership administration requires care and elevated privileges.

## Instructor preparation

Student exercises need only a normal account and a disposable directory. Prepare an isolated, snapshot-backed Ubuntu VM for the instructor demonstration of account and ownership administration. Do not perform account changes on a personal, classroom, or production host. Provide a way to restore the demonstration VM after class.

## Suggested lesson sequence

| Segment | Time |
|---|---:|
| Review and objectives | 10 min |
| Textbook and permission examples | 45 min |
| Instructor VM demonstration | 15 min |
| Student lab | 40 min |
| Knowledge check and reflection | 15 min |

# Textbook chapter

## 1. Identities name processes and files

Linux associates each process with a user identity. The login name is convenient for people, while the numeric UID is what the kernel uses to make access decisions. Groups work similarly: people see a group name, and the system associates it with a numeric GID. A user can belong to a primary group and may also have supplementary groups.

The `id` command reports the current user's UID, primary GID, and group memberships. `groups` prints group names associated with the current user, or with a named user when supplied. These commands inspect identity; they do not change accounts. The account and group records live in system-managed files such as `/etc/passwd` and `/etc/group`. Password data is protected separately. Students should not edit these files directly.

## 2. Reading ownership and permission bits

Use `ls -l` to display a long listing. A row includes an object type, permission bits, a link count, owner name, group name, size, timestamp, and name. For example:

```text
-rw-r----- 1 maya labteam 84 Nov 4 10:30 plan.txt
```

The first character `-` identifies a regular file. The next nine characters are three groups of permissions: owner (`rw-`), group (`r--`), and other users (`---`). In this example, Maya can read and write the file, members of `labteam` can read it, and other users have no listed permission. File ownership is one user and one group; it is not a list of every person who can access the file.

## 3. Read, write, and execute mean different things by object type

For a regular file, read permits viewing its contents, write permits changing its contents, and execute permits running it as a program (subject to other requirements). A text document normally does not need execute permission.

For a directory, read permits listing names, write permits creating or removing directory entries, and execute permits traversing the directory or accessing a known entry. Directory write permission without execute permission is generally not useful for ordinary navigation. To reach a file, a user needs traversal permission on each directory in its path as well as the needed permission on the file itself. Access can also be affected by parent directories, ACLs, mount options, and other controls; this lesson focuses on the basic owner/group/other model.

## 4. Changing permission bits with `chmod`

`chmod` changes permission bits on files the user owns, or on files for which the user has administrative authority. Symbolic notation names a class and an operation: `u` for owner, `g` for group, `o` for other, and `a` for all; `+` adds, `-` removes, and `=` sets the specified permissions. For example, `chmod g-w notes.txt` removes group write permission, and `chmod u+x script.sh` adds execute permission for the owner.

Octal notation represents read as 4, write as 2, and execute as 1. Add the values for each class: `7` means `rwx`, `6` means `rw-`, `5` means `r-x`, and `0` means `---`. Therefore `chmod 640 notes.txt` sets owner to read/write, group to read, and other to no access. This is appropriate for a private text file shared read-only with its group, but not a universal setting. Choose access based on the task and verify the result with `ls -l`.

Avoid changing permissions on system files or broad directory trees. In particular, do not use `chmod -R` on a system location or make a file broadly writable as a shortcut. The lab changes only files created inside its own practice directory.

The symbolic form can also set a class exactly: `chmod o=r notes.txt` makes the other class read-only while leaving owner and group permissions as they are. In octal notation, `chmod 755 program` means owner `rwx`, group `r-x`, and other `r-x`. A mode should reflect the actual task; `755` is not a safe default for every file, especially private data.

## 5. Default permissions and `umask`

When a program creates a file or directory, the requested starting permissions are reduced by the process's `umask`. The `umask` command without an argument displays the current mask. A mask is not itself a permission setting to apply to an existing file; it filters permissions from the creation defaults. For example, with a common mask of `022`, ordinary new files commonly start as `644` and directories as `755`, depending on the creating program's requested mode.

Common masks illustrate how defaults can be tightened. With typical creation requests, `022` commonly yields files with mode `644` and directories with mode `755`; `027` commonly yields `640` and `750`; `077` commonly yields `600` and `700`. Exact results can vary with the program's requested mode. A mask filters permission bits; it is not ordinary arithmetic subtraction from every existing file.

In this lesson, students inspect `umask` without changing their login configuration. A temporary change affects later creations in the current shell, so if an instructor chooses to demonstrate one, restore the prior value before ending the session. Do not edit startup files for this activity.

## 6. Owner and group are separate from permission bits

`chown` changes a file's owner and, when requested, its group. `chgrp` changes a file's group. On Ubuntu, changing a file to another user generally requires administrative privileges, and changing group ownership is limited to authorized cases. These commands can make data inaccessible or expose it to unintended readers if used carelessly.

The student lab does not use `chown` or `chgrp`. The instructor demonstrates them only on a dedicated file in the disposable VM. An ownership change does not replace permission bits: the new owner and group are evaluated against the existing permissions.

## 7. Special permission bits and access control lists

Linux has special permission bits in addition to the owner/group/other triplets. Set-user-ID (SUID) on an executable can make a process run with the effective user identity of the file's owner. It has security consequences and is not honored for every kind of executable; Linux ignores it on shell scripts. A sticky bit on a shared writable directory restricts who may remove or rename entries, typically allowing the entry's owner, the directory owner, or an administrator to do so. These bits are normally encountered on carefully managed system programs and shared directories. Students should understand their purpose, but must not set them on system files or practice them on a host.

The symbolic forms `chmod u+s program` and `chmod +t shared-directory` set these bits. They are shown for recognition only and are not lab instructions.

Access control lists (ACLs) can give named users or groups permissions beyond the basic owner, group, and other classes. ACLs add detail to the access model and can make a file's effective permissions less obvious from the three triplets alone. Their inspection and administration are advanced topics; this lesson focuses on the basic mode bits and ownership.

`getfacl file` displays the access ACL entries for a file or directory. An ACL can include a named user or group in addition to the owner and owning group. The ACL mask limits the effective permissions for group-class entries, including named users and groups, so the displayed entry and the effective access can differ. `setfacl -m u:username:r file` adds or modifies a named user's read entry; `setfacl -x u:username file` removes that named-user entry. These examples belong only in the instructor's disposable VM. This lesson does not use recursive ACL changes or remove every ACL entry at once.

## 8. Account and group administration belongs in a controlled environment

Administrators create accounts and groups with tools such as `useradd` and `groupadd`, and adjust membership or account properties with `usermod`. These operations can affect login access, home directories, and running services. Use them only on an approved disposable VM or a system for which the administrator has explicit responsibility. Never create practice accounts on a shared classroom computer or personal system as part of this lesson.

On Ubuntu, `useradd` is a lower-level account utility that accepts explicit options. The interactive `adduser` helper guides an administrator through account creation and applies Ubuntu defaults such as creating a home directory. Which tool to use depends on the administration task; neither is appropriate for an uncontrolled practice host. `sudo` runs an authorized command with elevated privileges and normally asks the invoking user to authenticate. Its allowed actions are controlled by administrator-managed policy, commonly in `/etc/sudoers`; do not edit that file directly.

In the VM demonstration, the instructor can create a temporary lab group and user, inspect their IDs, and add the user to the supplementary group. The VM is then restored from its snapshot. Students learn what the commands do and how to read the result; they do not need administrator access to meet this lesson's objectives.

## 9. A safe permission workflow

Before changing access, identify the exact file and its owner, group, and current permissions. Ask who needs access and what they need to do. Grant the smallest useful permission, make the change on one lab file, and verify it with `ls -l`. If the outcome is unclear, stop and inspect instead of adding broader access. This workflow supports least privilege: each user receives only the access needed for the task.

# Guided demonstrations

## Demonstration 1: Inspect identity and groups

```bash
id
groups
ls -l ~/linux-course/lesson4
```

Explain that `id` includes numeric IDs and group associations. Group membership alone does not grant access if the file or directory permission bits do not allow it.

## Demonstration 2: Symbolic permission change in a lab directory

```bash
mkdir -p ~/linux-course/lesson4/demo
cd ~/linux-course/lesson4/demo
printf 'Course notes\n' > notes.txt
ls -l notes.txt
chmod g-w notes.txt
ls -l notes.txt
```

Point out exactly which permission bit changed. Have learners predict the result before running `chmod`.

## Demonstration 3: Octal permission change

```bash
chmod 640 notes.txt
ls -l notes.txt
```

Translate each digit separately: owner `6` is `rw-`, group `4` is `r--`, and other `0` is `---`.

## Demonstration 4: Inspect the default mask

```bash
umask
touch new-file.txt
mkdir new-directory
ls -ld new-file.txt new-directory
```

Explain that the mask filters requested creation permissions, and that the file and directory may have different requested defaults.

## Demonstration 5: Instructor-only identity administration in a disposable VM

In a restored, isolated Ubuntu VM, the instructor may demonstrate:

```bash
sudo groupadd lesson4-demo
sudo useradd -m -s /bin/bash lesson4-student
sudo usermod -aG lesson4-demo lesson4-student
id lesson4-student
sudo chown lesson4-student:lesson4-demo /srv/lesson4-demo/shared.txt
sudo chgrp lesson4-demo /srv/lesson4-demo/shared.txt
ls -l /srv/lesson4-demo/shared.txt
```

This is instructor demonstration material only. The instructor should explain the effect of each command, confirm the VM snapshot is current, and restore the VM afterward. Never run this sequence on a personal or production host. The `-aG` form appends a supplementary group; omitting `-a` can replace existing supplementary memberships. The interactive `adduser` helper may also be shown as the Ubuntu-friendly account-creation alternative.

## Demonstration 6: Inspect and modify an ACL in the disposable VM

Prepare `/srv/lesson4-demo/shared.txt` in the VM, with `lesson4-student` as its owner, and ensure `lesson4-reader` is a separate demo account. Then demonstrate:

```bash
sudo setfacl -m u:lesson4-reader:r /srv/lesson4-demo/shared.txt
getfacl /srv/lesson4-demo/shared.txt
sudo setfacl -x u:lesson4-reader /srv/lesson4-demo/shared.txt
getfacl /srv/lesson4-demo/shared.txt
```

Have students identify the named-user entry and the ACL mask in the output, then observe that the entry is gone after `-x`. This changes only the prepared VM file. Restore the VM snapshot after class.

# Student lab: Lesson 4 — Users, Groups, and Permissions

## Safety and scope

Use a normal, non-administrator account. Work only in `~/linux-course/lesson4`. Do not use `sudo`, do not change ownership, and do not modify account or system configuration. If a permission change behaves unexpectedly, stop and ask the instructor rather than applying broader access.

## Part A: Inspect your identity

```bash
id
groups
```

Record your UID, primary GID, and one group name. Explain why the system uses numeric IDs even though people often use names.

## Part B: Create a permission practice area

```bash
mkdir -p ~/linux-course/lesson4
cd ~/linux-course/lesson4
printf 'A private course note.\n' > private.txt
printf '#!/bin/sh\nprintf "Lab script\\n"\n' > lab-script.sh
ls -l
```

Identify the owner, group, and owner/group/other permission triplets. Note that the script content does not automatically make it executable.

## Part C: Set permissions symbolically

```bash
chmod u+x lab-script.sh
chmod o-r private.txt
ls -l
```

For each command, name the class, operation, and permission being changed. Explain why the script receives execute permission while the private note loses read permission for other users.

## Part D: Set permissions with octal notation

```bash
chmod 640 private.txt
chmod 750 lab-script.sh
ls -l
```

Translate `640` and `750` into owner, group, and other permissions. Verify the listing matches your translation.

## Part E: Inspect default creation behavior

```bash
umask
touch mask-check.txt
mkdir mask-check-directory
ls -ld mask-check.txt mask-check-directory
```

Compare the displayed permissions. Explain why `umask` is a filter on new objects rather than a command that edits the permissions of existing files.

## Part F: Read ownership and ACL examples

Do not run ownership or ACL-changing commands. Review the instructor's VM output for `chown`, `chgrp`, `useradd`, `groupadd`, `usermod`, `setfacl`, and `getfacl`. For each, state which identity, ownership, or access property it inspects or changes and why the operation belongs in an approved administrative environment.

## Lab reflection

1. What is the difference between a UID and a username?
2. For a directory, what does execute permission allow?
3. What permission set does `chmod 640 file` express?
4. Why should supplementary group membership be modified with care?
5. Why are ownership commands excluded from the student lab?

## Lab success criteria

- The student can interpret `id`, `groups`, and a long listing.
- The student correctly sets and verifies the requested symbolic and octal permissions.
- The student can explain how directory execute permission differs from file execute permission.
- The student can describe the purpose and risk boundary for each instructor-demonstrated account or ownership command.

# Knowledge check

1. What numeric identity does the kernel use for a user?
2. Which command reports the current user's UID, primary GID, and groups?
3. What does the `groups` command show?
4. In `ls -l`, what do the owner, group, and other triplets describe?
5. For a directory, what does write permission generally allow?
6. What does `chmod g-w file.txt` do?
7. What permissions does octal digit `5` represent?
8. Translate `chmod 640 file.txt` into owner, group, and other permissions.
9. What does `umask` affect?
10. Which command changes a file's owner?
11. Which command changes a file's group?
12. What does `useradd` do?
13. How does Ubuntu's `adduser` helper differ from `useradd`?
14. What does `groupadd` do?
15. Why should `usermod -aG group user` include `-a` when adding a supplementary group?
16. What does `chmod u+s program` set, and why is it sensitive?
17. What does a sticky bit on a shared writable directory help control?
18. What additional access can an ACL describe?
19. What do typical masks `027` and `077` commonly produce for new files and directories?
20. Why must account and ownership demonstrations use a disposable VM?
21. What does `getfacl file` display?
22. What does `setfacl -m u:reader:r file` do?
23. What does `setfacl -x u:reader file` do?
24. What is the purpose of the ACL mask?

## Answer key

1. The numeric UID.
2. `id`.
3. The group memberships associated with the current user, or a named user when supplied.
4. Permissions for the file's owner, members of its group, and other users, respectively.
5. It generally permits creating or removing directory entries, subject to other required permissions such as execute/traverse.
6. Removes group write permission from the file.
7. Read and execute (`r-x`).
8. Owner read/write (`rw-`), group read (`r--`), other no permissions (`---`).
9. It filters requested permissions on newly created files and directories; it does not directly edit existing objects.
10. `chown`.
11. `chgrp`.
12. It creates a user account.
13. It is an interactive Ubuntu helper that guides account creation and applies defaults such as creating a home directory.
14. It creates a group.
15. `-a` appends the group instead of replacing the user's existing supplementary group list.
16. It sets SUID; for an executable it can cause a process to use the file owner's effective user identity, so it has security consequences.
17. It helps restrict who may remove or rename entries in a shared writable directory.
18. Permissions for named users or groups beyond the basic owner/group/other classes.
19. Typically `027` yields files `640` and directories `750`; `077` yields files `600` and directories `700`, depending on the creating program's requested mode.
20. They alter system identities and ownership, can affect access or login, and require administrative authority; a disposable VM can be restored safely.
21. The file's access control list entries, including any named-user or named-group entries.
22. It adds or modifies a named user's read permission entry for the file.
23. It removes that named-user ACL entry.
24. It limits effective permissions for group-class ACL entries, including named users and groups.

## Exit ticket

Before you change a permission, what two things should you verify about the target and the access it needs?

# Chapter glossary

**Group:** A named collection of users represented internally by a numeric GID.  
**Group ID (GID):** The numeric identifier associated with a group.  
**Access control list (ACL):** Additional per-user or per-group permission entries beyond the basic owner/group/other classes.  
**ACL mask:** The maximum effective permission set for group-class ACL entries.  
**Least privilege:** Giving an identity only the access needed for its task.  
**Ownership:** The user and group associated with a filesystem object.  
**Permission bits:** The read, write, and execute settings for owner, group, and other users.  
**Primary group:** The main group associated with a user account.  
**Set-user-ID (SUID):** A special permission bit that can cause an applicable executable to use the file owner's effective user identity.  
**Sticky bit:** A directory permission bit that restricts removal or renaming of entries in a shared writable directory.  
**Supplementary group:** An additional group membership associated with a user.  
**UID:** Numeric user identifier used by the system for access decisions.  
**Umask:** A process setting that filters permissions requested when new objects are created.  

# Instructor materials

## Instructor notes

- Keep student practice unprivileged and limited to the lab directory.
- Explain that directory execute means traversal/search, not “run the directory.”
- Distinguish identity (UID/GID), ownership, and permission bits; changing one does not automatically change the others.
- Do not teach broad permission shortcuts such as making files world-writable.
- Run account and ownership demonstrations only in a dedicated, snapshot-backed VM and restore it after class.
- Explain that `usermod -aG` appends a supplementary group, while omitting `-a` can discard other supplementary memberships.
- Mention ACLs and other access controls as topics beyond the basic model, without implying that the three permission triplets are the entire security system.
- Keep `getfacl` and `setfacl` demonstrations in the disposable VM; explain the ACL mask when reading output and avoid recursive or remove-all operations.
- Treat SUID and sticky bits as recognition topics only; do not ask students to set them on a host or system file.
- Qualify common `umask` outcomes as dependent on the mode requested by the creating program.

## Suggested slide outline

1. User and group identity: names and numeric IDs
2. Inspecting identity with `id` and `groups`
3. Reading `ls -l` ownership and permission fields
4. File permissions: read, write, execute
5. Directory permissions: listing, changing entries, traversal
6. Symbolic `chmod` notation
7. Octal `chmod` notation
8. `umask` and creation defaults
9. Ownership with `chown` and `chgrp`
10. Special permission bits and ACLs
11. Account administration in a disposable VM
12. Least privilege and verification

## Commands introduced

`id`, `groups`, `chmod`, `umask`, `chown`, `chgrp`, `useradd`, `adduser`, `groupadd`, `usermod`, `getfacl`, and `setfacl`. `ls -l` and `sudo` are reused from earlier lessons. Account, ownership, and ACL-changing commands are instructor demonstration material in a disposable VM only. Special permission bits are conceptual extensions, not student lab operations.
