# Lesson 11 — Services and System Administration

## Lesson purpose

Linux systems use hardware devices and background services to provide computing, storage, networking, printing, and other functions. This lesson introduces safe hardware discovery and driver inspection, then explains systemd as a service and system manager, unit files, startup relationships, service state, and logs. Students practice with a harmless user-level unit that they can start and stop themselves.

## Learning objectives

By the end of the lesson, students will be able to:

- distinguish a service, process, systemd unit, and unit file;
- describe the main Linux boot stages and systemd's place in them;
- use `systemctl get-default` and current-boot journal output as read-only boot evidence;
- distinguish a unit's active state from its enabled state;
- inspect services with `systemctl` and view logs with `journalctl`;
- explain the difference between start, restart, reload, and daemon-reload;
- read a simple service unit file and describe its basic sections;
- inspect, start, restart, and stop a harmless user-level service;
- use service status and logs as evidence during a basic troubleshooting sequence;
- explain the high-level firmware-to-userspace boot sequence and inspect current-boot journal evidence; and
- identify common hardware buses and inspect devices, kernel drivers, modules, and device events without changing configuration.

## Instructor preparation

Use Ubuntu Desktop LTS or a disposable course VM with systemd and a logged-in graphical user session. Ensure `pciutils` (`lspci`) and `usbutils` (`lsusb`) are available; preinstall them in the course image if needed. Place the accompanying `lesson-11-demo.service` file in the student practice directory. It starts only `/usr/bin/sleep infinity` as the student's own user; it does not listen on a network port or change system configuration. Confirm `systemctl --user` works in the classroom environment. If the user systemd manager is unavailable, provide instructor-generated status and journal output or use a disposable VM with an active login session. Do not use the lab to restart, stop, enable, disable, or edit system services.

## Suggested lesson sequence

| Segment | Time |
|---|---:|
| Review processes and network sockets | 10 min |
| Hardware, boot process, textbook, and guided demonstrations | 55 min |
| Student lab: hardware inventory and user-level unit | 55 min |
| Knowledge check and reflection | 15 min |

# Textbook chapter

## 1. From process to service

A **process** is a running instance of a program. A **service** is a program or group of programs that provides a continuing function, often without a person interacting with it directly. A service is usually represented by one or more processes, but service and process are not interchangeable terms: one service can manage several processes, and a process may be an ordinary application rather than a system service.

Lesson 10 introduced listening sockets. A service may listen on a local or network address, but many services do not use network sockets at all. A process can exist without successfully providing its intended service. Service management tools help answer questions such as whether a service is loaded, active, enabled for startup, or in a failed state.

## 2. systemd and unit types

On most current Ubuntu installations, **systemd** is the system and service manager that runs as process ID 1 after the kernel starts it. It helps initialize the system, start units in an order based on dependencies and conditions, supervise services, and collect system logs through the journal. The boot process also involves firmware, a bootloader, and the kernel; systemd is one stage in the larger startup path.

A **unit** is a resource or action that systemd can manage. Common unit types include:

| Unit type | Purpose |
|---|---|
| `.service` | Start and supervise a service process |
| `.socket` | Represent a listening socket and possibly activate a service when used |
| `.target` | Group units into a system state or synchronization point |
| `.timer` | Activate a unit at specified times or intervals |
| `.mount` | Represent a filesystem mount |

Unit files describe how units are configured. A unit can be supplied by a package, created by an administrator, or created for one user's session. System-level units are generally managed by the system manager and often need administrator privileges for changes. A user manager handles units for a particular account; `systemctl --user` addresses that manager and does not mean “run as root.”


## 2a. The Linux boot process

The boot process connects firmware, boot software, the Linux kernel, an early temporary filesystem, and the userspace service manager. The exact details vary with hardware, firmware mode, bootloader, distribution, and storage setup. Some UEFI configurations can load an operating system kernel directly; the diagram shows a common bootloader-based path, not an unchanging rule. A useful high-level sequence is:

```text
Power on
  → BIOS or UEFI firmware initializes hardware and selects a boot path
  → bootloader (often GRUB) selects and loads a kernel and initramfs
  → Linux kernel initializes and starts early userspace from the initramfs
  → initramfs locates and mounts the real root filesystem, then hands off
  → systemd (commonly PID 1) starts the default target and its units
  → login screen or other user-facing system is ready
```

**Firmware** begins the bootstrap process by initializing hardware and selecting a boot device or entry. Older computers commonly use BIOS; modern computers commonly use UEFI. Some machines can boot from network services using PXE. An ISO image is a disk image commonly used to make installation or recovery media; it is not a required stage of every normal boot.

A **bootloader**, often GRUB on Ubuntu, presents or follows a boot entry and loads the selected kernel image and initramfs into memory. GRUB can support multiple operating systems and kernel choices. Some older GRUB descriptions use stages 1, 1.5, and 2; that staging describes particular legacy BIOS installation layouts and is not a universal sequence for modern UEFI systems. Firmware mode, partition scheme, and bootloader installation determine the details. Students should not edit GRUB configuration or reinstall a bootloader as part of this lesson.

The **kernel** initializes its core subsystems and hardware support. The **initramfs** (initial RAM filesystem, also historically called initrd) provides a temporary early-userspace environment. It can load required modules and locate storage needed for the real root filesystem, including more complex storage configurations. Early userspace then switches to the actual root filesystem and starts the system's init process. On most Ubuntu systems, that process is systemd as PID 1. Kernel image names such as `vmlinuz` and initramfs image names such as `initrd.img` are common conventions, not names that define separate boot stages.

Systemd then activates the default target and the units connected through dependencies and ordering. Services, devices, mounts, sockets, and other units may be started as the system moves toward the target. A graphical system eventually presents a login screen; a server may present a text login or run without a local interactive login. This connects directly to the unit and service material later in this lesson.

Older Unix-like systems commonly described startup states with **System V runlevels**. Systemd uses **targets**. Some runlevel names map to familiar targets for compatibility, but the models are not identical; describe targets using systemd's own concepts rather than treating every runlevel as a one-to-one match.

A **kernel panic** is a critical kernel error from which the kernel cannot safely continue. It differs from an initramfs prompt caused by the inability to find or mount the real root filesystem, and both differ from a single service failing after userspace has started. That distinction helps locate the stage that may need investigation.

Two read-only commands can help orient an investigation:

```bash
systemctl get-default
journalctl -b -p err --no-pager
```

`systemctl get-default` reports the configured default target. `journalctl -b` filters journal messages to the current boot, and `-p err` selects error-priority messages and more severe priorities. Journal access and retained boot history vary; an ordinary user may not see all system messages. Do not use `sudo` to bypass those permissions in this course. A displayed error is evidence to investigate, not proof by itself that boot failed.

## 2b. Hardware discovery and management in Linux

Linux separates **user space**, where ordinary applications and administrative tools run, from **kernel space**, where the kernel and device drivers operate with greater privilege. User-space tools request information through defined kernel interfaces; they do not need direct access to device registers to list hardware. A **device driver** is kernel software that communicates with a particular device or class of devices and exposes its functions to the operating system.

Devices connect through buses such as PCI Express (PCIe) and USB. A host bus adapter (HBA) is a controller connecting a system to a storage interface or fabric; it may appear as a PCI device. In a virtual machine, the guest usually sees virtual devices, so its inventory may not match the physical host. Device IDs and names can help identify hardware, but a local ID database may not have a friendly description for every device.

Read-only inventory tools complement earlier lessons:

```bash
lspci -nnk
lsusb -t
lsblk
```

`lspci -nnk` lists PCI devices with vendor/device IDs and, when available, the kernel driver currently handling each device and modules that could handle it. `lsusb -t` presents USB devices in a tree. `lsblk` lists block devices, which Lesson 9 used to inspect storage. Some PCI details may be restricted, and virtual machines may not expose physical host devices.

The **sysfs** virtual filesystem at `/sys` presents relationships and attributes for kernel devices, buses, classes, and drivers. `/sys/bus` groups devices by bus, `/sys/class` groups them by function, and `/sys/block` represents block devices. These are interfaces to kernel objects, not ordinary storage directories. Some sysfs attributes are writable and can change live system behavior; this lesson only inspects them and does not write to `/sys`.

Linux represents many devices with special device nodes under `/dev`. These are interfaces to device drivers, not hardware files to edit. On Ubuntu, **udev** (provided by systemd-udevd) handles device events and applies rules that manage device nodes, names, symlinks, and permissions. `udevadm info` queries the udev database for a device's properties; it does not trigger a rescan or reconfigure the device:

```bash
udevadm info --query=all /dev/DEVICE
```

Replace `/dev/DEVICE` only with an existing device path identified in the lab, such as one shown by `lsblk`. Device paths vary. Do not run `udevadm trigger` or edit udev rules in this course.

Some drivers are built into the kernel; others are loadable **kernel modules**. `lsmod` lists modules currently loaded in the kernel, and `modinfo MODULE` displays metadata for a named module. A driver built into the kernel will not appear in `lsmod`. A module being loaded does not by itself prove which device it currently handles; `lspci -k` may show that relationship for PCI devices. Do not load, unload, blacklist, or install drivers in this lesson.

Hardware also communicates with the processor and memory. An **interrupt request (IRQ)** is a signal or message asking the processor to handle an event. Modern systems route interrupts through platform interrupt controllers, and allocation is managed by the kernel and firmware; IRQ numbers are not a simple fixed priority ranking. **Direct memory access (DMA)** lets a capable device transfer data to or from memory without the CPU copying every byte. Drivers and the kernel coordinate safe DMA mappings, sometimes with hardware such as an IOMMU. These mechanisms are useful concepts, but students do not configure interrupt or DMA settings.

This is hardware **discovery and observation**, not hardware reconfiguration. The lesson does not ask students to run `modprobe`, `rmmod`, `udevadm trigger`, `hdparm` configuration, network-interface changes, printer-administration commands, or writes to `/sys`. Those operations can affect a live system and require a separate, authorized procedure.

## 3. Inspect unit state with systemctl

`systemctl` communicates with a systemd manager. Its inspection commands can show unit state without changing it:

```bash
systemctl --user status lesson11-course-demo.service --no-pager
systemctl --user list-units --type=service --state=running
systemctl --user is-active lesson11-course-demo.service
systemctl --user is-enabled lesson11-course-demo.service
```

`status` gives a summary such as whether the unit is loaded, active, or failed, its main process when relevant, and recent log messages. `list-units` lists units currently known to the manager; it does not necessarily list every installed unit file. `is-active` asks whether a unit is active now. `is-enabled` reports whether a unit file has been configured for automatic activation through an installation target. Output varies by unit and systemd version.

A service can be **active but disabled**: it is running now but is not configured to start automatically at boot or login. It can be **enabled but inactive**: it is configured for automatic activation, but is not running now. Enabled and active describe different properties. Some units are static, generated, transient, or activated by another unit and may not have a simple enabled/disabled state.

## 4. Start, stop, restart, reload, and enable

The common service control actions have distinct meanings:

- `start` asks the manager to activate a unit now.
- `stop` asks it to deactivate a unit now.
- `restart` stops and starts it again. This may briefly interrupt the service.
- `reload` asks the service itself to reread its application configuration, if it supports that operation. It does not reread the systemd unit file.
- `enable` configures a unit to be activated through the appropriate startup target or trigger. It does not necessarily start it immediately; `--now` combines enablement and starting.
- `disable` removes that automatic activation configuration; it does not necessarily stop a currently running service.

Changes to unit files require `systemctl daemon-reload` so the manager re-reads unit definitions. For a user unit, use `systemctl --user daemon-reload`. This action does not restart the service. It is different from `systemctl reload UNIT`, which asks the application service to reload its own configuration.

These are state-changing actions. In this lesson, students may run start, restart, and stop only on the course-provided user unit. They must not manage system services or enable the demonstration unit for login startup.

## 5. Read a simple unit file

The accompanying `lesson-11-demo.service` file contains:

```ini
[Unit]
Description=Lesson 11 Harmless User Service

[Service]
Type=simple
ExecStart=/usr/bin/sleep infinity

[Install]
WantedBy=default.target
```

`[Unit]` holds general description and dependency information. `[Service]` defines how the program is started and supervised. `Type=simple` is suitable for a process that stays in the foreground. `ExecStart` names the program and its arguments; systemd does not interpret this line as a shell command by default. `[Install]` describes how enablement could connect the unit to a target; those activation links are not created until the unit is enabled. The lab deliberately does not enable it.

This sample runs `sleep infinity` and has no network listener, privileged command, or application data. It is intentionally minimal so students can focus on unit management. Real service unit files may include security restrictions, environment, dependencies, restart policies, credentials, and other details; copying a sample unit into production without understanding it is not appropriate.

## 6. Dependencies, ordering, and startup

Systemd uses dependencies and ordering relationships to determine which units to activate and when. For example, one unit can require another unit, or be ordered to start after a prerequisite. A requirement does not always mean the same thing as ordering: dependency directives describe relationships, while ordering directives describe sequencing. Units can start in parallel when their relationships allow it.

Targets group units into a desired system state, such as a graphical or multi-user state. During boot, systemd activates a default target and the units connected to it. A unit's `WantedBy=` setting in `[Install]` helps `enable` create links that make the unit wanted by a target. This is why enablement is separate from simply starting a process. Students should understand the relationship, but do not change the system's default target or enable services in the lab.

## 7. Inspect logs with journalctl

The **systemd journal** collects messages from the kernel, services, and other system components. `journalctl` reads those messages. Permissions and journal configuration determine which entries a normal user can see; not every system log is available to every account, and persistent storage across reboots depends on system configuration.

Use filters to keep output relevant:

```bash
journalctl --user -u lesson11-course-demo.service -n 20 --no-pager
journalctl --user -u lesson11-course-demo.service --since today --no-pager
```

`-u` filters by unit, `-n 20` limits output to the most recent 20 entries, `--since today` limits the time range, and `--no-pager` prints directly rather than opening an interactive pager. The `--user` option selects the user's journal. For a system service, `journalctl -u NAME.service` is common, but access can be restricted and may require authorization. Do not use `sudo` to work around a permissions boundary in this course.

Logs provide evidence, not an automatic explanation. Read timestamps, unit names, severity, and the actual message. A service may fail because of invalid configuration, missing files, access permissions, unavailable dependencies, or resource limits. Avoid posting logs publicly until they have been checked for usernames, hostnames, addresses, tokens, and other sensitive data.

## 8. A safe service troubleshooting sequence

When an application or service appears unavailable, begin by defining the symptom. Use a small set of checks and record the time:

1. Check whether the expected unit exists and whether it is loaded.
2. Inspect whether it is active, inactive, or failed with `systemctl status` or `systemctl is-active`.
3. Read recent messages for that unit with a narrow `journalctl` filter.
4. Compare the service process and its socket behavior with the expected design; Lesson 5 covers processes and Lesson 10 covers local listening sockets.
5. Form one hypothesis and make only an authorized change to the lab unit, then inspect status and logs again.

Do not restart services at random. A restart can interrupt users or erase useful diagnostic state. Do not enable a service simply because it is inactive: first establish whether it is intended to run now and whether startup activation is required. In a production environment, follow the change procedure and obtain the required authorization.

# Guided demonstrations

## Demonstration 1: Inspect user units

```bash
systemctl --user list-units --type=service --state=running
systemctl --user status lesson11-course-demo.service --no-pager
```

Before the student unit is installed, the named unit may be reported as not found. Use that result to explain the difference between “not installed/loaded” and “failed.”

## Demonstration 2: Compare active and enabled states

```bash
systemctl --user is-active lesson11-course-demo.service
systemctl --user is-enabled lesson11-course-demo.service
```

After starting (but not enabling) the unit, show that it can be active and disabled. Explain that `is-enabled` may return different state labels for static or transient units.

## Demonstration 3: Read unit-specific journal entries

```bash
journalctl --user -u lesson11-course-demo.service -n 20 --no-pager
```

Point out the timestamps and start/stop messages. The exact messages depend on systemd version and journal settings.


## Demonstration 4: Connect systemd to the current boot

```bash
systemctl get-default
journalctl -b -p err --no-pager
```

Explain the active boot filter, priority selection, and possible limits on user-visible system logs. Do not attempt to repair or clear an error during this orientation; use the entry as evidence and describe which boot stage it might relate to.

## Demonstration 5: Inspect devices and their drivers

```bash
lspci -nnk
lsusb -t
lsblk
lsmod
ls /sys/bus /sys/class /sys/block
journalctl -k -b -n 30 --no-pager
```

Compare the bus inventories with the block-device list. In a virtual machine, explain that these are guest-visible devices. In PCI output, distinguish the kernel driver currently handling a device from modules that could handle it. Do not run a configuration or rescan command.

# Student lab: Lesson 11 — Manage a Harmless User Service

## Safety and scope

Use your own course VM user account. The provided unit runs `/usr/bin/sleep infinity` as your user and does not listen on a port. Do not use `sudo`, copy this unit into `/etc/systemd`, stop or restart any other service, enable the unit, or change boot targets. Use the exact unit name `lesson11-course-demo.service`. At the end, stop the unit and remove the user unit file you installed.

## Part A: Install the course unit

From the directory containing the provided `lesson-11-demo.service`, inspect its contents, then create the user unit directory and copy it into place:

```bash
mkdir -p ~/.config/systemd/user
cp lesson-11-demo.service ~/.config/systemd/user/lesson11-course-demo.service
systemctl --user daemon-reload
```

`daemon-reload` asks the user manager to reread unit files. It does not start the service. If the destination file already exists, do not overwrite it; ask the instructor for guidance.

## Part B: Inspect before starting

```bash
systemctl --user status lesson11-course-demo.service --no-pager
systemctl --user is-active lesson11-course-demo.service
systemctl --user is-enabled lesson11-course-demo.service
```

Record the reported state. It should not be active yet. It should not be enabled. Depending on systemd version, an inactive or not-found-style result can vary before the first load; discuss the actual evidence with the instructor.

## Part C: Start and verify the service

```bash
systemctl --user start lesson11-course-demo.service
systemctl --user status lesson11-course-demo.service --no-pager
systemctl --user is-active lesson11-course-demo.service
```

Confirm that status shows the unit active and that the main process is the expected `sleep` program. Explain why the service can be active while still disabled.

## Part D: Restart and inspect logs

```bash
systemctl --user restart lesson11-course-demo.service
journalctl --user -u lesson11-course-demo.service -n 20 --no-pager
```

Find a recent start or stop event and record its timestamp. Explain what restart did and why log output may vary.

## Part E: Stop and clean up

```bash
systemctl --user stop lesson11-course-demo.service
systemctl --user is-active lesson11-course-demo.service
rm -- ~/.config/systemd/user/lesson11-course-demo.service
systemctl --user daemon-reload
systemctl --user status lesson11-course-demo.service --no-pager
```

Confirm that the service is no longer active. The final status may report that the unit is not found because its unit file has been removed. The `rm` command here targets only the exact course-created file; do not broaden or modify the path.

## Part F: Inventory guest-visible hardware

Run these read-only commands:

```bash
lspci -nnk
lsusb -t
lsblk
lsmod
ls /sys/bus /sys/class /sys/block
journalctl -k -b -n 30 --no-pager
```

Record one PCI device and, if the output provides it, its current kernel driver. Record whether the VM exposes any USB devices. Compare `lsblk` output with the storage inspection from Lesson 9. Use the `/sys` listings to identify the bus, class, and block-device views. If visible, note one recent kernel message related to a device; do not infer that every message indicates a fault. If `lspci` or `lsusb` is unavailable, the instructor can provide a transcript; do not install packages during this lab.

If the instructor selects a specific module that appears in `lsmod`, inspect its metadata:

```bash
modinfo MODULE
```

Replace `MODULE` with the selected module name. `modinfo` describes a module; it does not prove that the module handles a particular device. For a device node actually present in `lsblk`, optionally inspect udev properties:

```bash
udevadm info --query=all /dev/DEVICE
```

Substitute only an existing device path identified by `lsblk`. Do not use `sudo`, run `udevadm trigger`, load or unload a module, or write under `/sys`.

## Part G: Observe boot and target information

Run the following read-only commands:

```bash
systemctl get-default
journalctl -b -p err --no-pager
```

Record the default target and one relevant current-boot entry if visible. If the journal is empty, access is restricted, or no error entry appears, state that plainly; do not use `sudo`. Explain why one error-priority message does not necessarily mean the system failed to boot.

## Part H: Write a short service and hardware report

Record:

1. The unit's state before start, after start, and after stop.
2. The difference between active and enabled for this unit.
3. One log entry and what it establishes.
4. Why `daemon-reload` was needed after placing and removing the unit file.
5. One thing the status and log output do not prove about a hypothetical network service.
6. Which boot stage does systemd follow, and what does `systemctl get-default` tell you?
7. Identify one PCI or USB device and, where shown, its kernel driver. State whether your VM exposes physical or virtual hardware.
8. Explain the difference between a loaded module and a driver shown as currently handling a device.

## Lab success criteria

- The student reads a user unit file and explains its basic directives.
- The student correctly distinguishes active state from enablement.
- The student starts, restarts, inspects, and stops only the provided user unit.
- The student filters journal output by unit and time/quantity, and interprets current-boot evidence without overstating what it proves.
- The student removes the exact demonstration unit file and reloads the user manager.
- The student identifies the default target and describes current-boot journal limits without escalating privileges.
- The student identifies guest-visible PCI/USB hardware, interprets driver/module output, and reads `/sys` or kernel-log evidence without changing configuration.
- No system service, boot target, hardware device, or system-wide unit configuration is changed.

# Knowledge check

1. How does a service differ from a process?
2. What is systemd's role on a typical current Ubuntu system?
3. What is a systemd unit?
4. Name three systemd unit types and their purposes.
5. What is the purpose of `systemctl`?
6. What does `systemctl status UNIT` summarize?
7. What does `systemctl is-active UNIT` report?
8. What does `systemctl is-enabled UNIT` report?
9. How can a service be active but disabled?
10. Does `systemctl enable UNIT` necessarily start the service now?
11. What does `systemctl start UNIT` do?
12. What does `systemctl restart UNIT` do, and what is one impact?
13. How does application `reload` differ from `daemon-reload`?
14. Why does a user unit use `systemctl --user`?
15. What is the role of the `[Service]` section?
16. What does `ExecStart=` specify in the lesson's unit file?
17. What is a target used for?
18. What is the purpose of `journalctl`?
19. What does `journalctl -u UNIT` filter?
20. What does `-n 20` do in a `journalctl` command?
21. Why might a normal user be unable to read some system journal entries?
22. Why should students not restart an arbitrary system service?
23. What is the purpose of `systemctl daemon-reload` after changing a unit file?
24. Why are timestamps useful when reading service logs?
25. What should a student do if the provided user manager is unavailable?
26. Which unit does the lab permit students to start and stop?
27. What role does firmware play in booting a Linux system?
28. What does the bootloader load for Linux to begin starting?
29. What is the initramfs used for?
30. What does `systemctl get-default` report?
31. What does `journalctl -b -p err --no-pager` select?
32. How does a kernel panic differ from a single service failure after userspace starts?
33. What does `lspci -nnk` show beyond a basic PCI device list?
34. What does `lsusb -t` display?
35. How does `lsmod` differ from `modinfo MODULE`?
36. What does `udevadm info --query=all /dev/DEVICE` query?
37. What do `/sys/bus`, `/sys/class`, and `/sys/block` expose?
38. What is an IRQ?
39. What does DMA let a capable device do?
40. Why should students not run `udevadm trigger` or write to `/sys` in this lesson?
41. What does `journalctl -k -b -n 30 --no-pager` display?

## Answer key

1. A service provides a continuing function and is often managed as a unit; a process is a running program instance. A service can involve one or more processes.
2. It is the system and service manager, commonly PID 1, that initializes the system and manages units.
3. A resource or action that systemd manages, described by unit configuration and runtime state.
4. Examples: `.service` manages a service process; `.socket` represents a socket; `.target` groups units; `.timer` activates a unit by time; `.mount` represents a mount.
5. It communicates with a systemd manager to inspect or control units.
6. Load and runtime state, process details when relevant, and recent log messages.
7. Whether the unit is active now.
8. Whether the unit file is configured for automatic activation through an installation target or trigger.
9. It has been started manually or by another trigger but is not configured for automatic startup through enablement.
10. No. Enablement configures automatic activation; starting is a separate action unless `--now` is used.
11. It asks the manager to activate the unit now.
12. It stops and starts the unit again; this may briefly interrupt its function.
13. A service reload asks the application to reread its own configuration if supported. `daemon-reload` makes systemd reread unit definitions.
14. It addresses the systemd manager for the current user's session, not the system-wide manager.
15. It holds service-specific settings such as type and start command.
16. The executable and arguments that systemd should launch for the service.
17. It groups units into a state or synchronization point and can participate in startup activation.
18. It reads and filters messages collected in the systemd journal.
19. Journal records associated with the specified unit.
20. It limits displayed entries to the latest 20.
21. Journal permissions and configuration limit which records the account can access.
22. It may interrupt users or affect system operation, and the student may not have authorization or understand its dependencies.
23. It asks the manager to reread unit files; it does not itself restart the service.
24. They help correlate events with reported symptoms and establish sequence.
25. Use instructor-provided output or a disposable VM with a working user systemd manager; do not use `sudo` to bypass the lab boundary.
26. `lesson11-course-demo.service`, the harmless user-level unit supplied for the course.
27. It initializes hardware and chooses a boot device or entry such as a disk or network boot path.
28. It loads the selected kernel and initramfs into memory and transfers control to the kernel.
29. It provides early userspace that can load necessary modules and locate/mount the real root filesystem before handing off.
30. The target unit that the system is configured to use as its default boot target.
31. Journal entries from the current boot at error priority or more severe, printed without a pager.
32. A kernel panic prevents the kernel from continuing safely; a service failure happens later, after userspace has started, and can be limited to that unit.
33. PCI vendor/device IDs and, when available, the driver currently handling the device and compatible modules.
34. The USB device hierarchy as a tree.
35. `lsmod` lists currently loaded modules; `modinfo` displays metadata for a named module.
36. It queries udev database properties for an existing device path.
37. `/sys` kernel-device views organized by bus, class, and block-device function.
38. An interrupt signal or message that requests processor attention for an event.
39. Transfer data to or from memory without the CPU copying every byte; the kernel and drivers coordinate the operation.
40. They can trigger device events or alter live kernel/device behavior, outside this lab's read-only scope.
41. Up to the most recent 30 kernel journal messages from the current boot, printed without a pager.

## Exit ticket

A service is active, but a user reports that its application is unavailable. Name two pieces of evidence you would inspect next and one conclusion that status alone cannot support.

# Chapter glossary

**BIOS/UEFI:** Firmware interfaces that initialize a system and begin its boot path.
**Bootloader:** Software such as GRUB that selects and loads a kernel and initial RAM filesystem.
**Initramfs:** A temporary early-userspace filesystem used during startup to prepare and mount the real root filesystem.
**Kernel panic:** A critical kernel error that prevents the kernel from safely continuing.
**Device driver:** Kernel software that communicates with a device and exposes its functions to the operating system.
**Kernel module:** Loadable kernel code; some drivers are modules, while others are built into the kernel.
**sysfs:** A virtual filesystem exposing kernel objects and their relationships through `/sys`.
**udev:** Device management in user space that processes kernel device events and manages device nodes and rules.
**IRQ:** An interrupt request that notifies the processor of an event.
**DMA:** A coordinated method for a capable device to transfer data to or from memory without CPU copying of every byte.
**Active:** A current runtime state indicating a unit is running or otherwise activated.  
**Daemon:** A background process that provides a service or system function.  
**Enabled:** Configured for automatic activation by a target or trigger.  
**Journal:** systemd's structured log store for system and service messages.  
**Service:** A program or group of programs that provides a continuing function.  
**systemd:** A system and service manager commonly used as PID 1 on Ubuntu.  
**Target:** A systemd unit that groups other units into a state or synchronization point.  
**Unit:** A systemd-managed resource or action, such as a service, socket, target, timer, or mount.  
**Unit file:** Configuration that describes a systemd unit.  

# Instructor materials

## Instructor notes

- Connect Lesson 11 to Lesson 1's hardware/kernel overview, Lesson 3's `/dev` and `/sys`, Lesson 5's process model, Lesson 9's storage inventory, and Lesson 10's network devices.
- Use the boot-sequence diagram to relate firmware, bootloader, kernel/initramfs, and systemd; note that GRUB's legacy BIOS stages are not universal.
- Explain that `lspci` and `lsusb` show devices visible to the current host or VM, not necessarily all physical host hardware.
- Distinguish modules merely capable of handling a PCI device from the kernel driver currently handling it.
- Keep `/sys`, udev, IRQ, and DMA discussion conceptual and read-only; do not demonstrate trigger, load/unload, or configuration operations.
- Explain that System V runlevels are historical concepts with approximate systemd target compatibility, not a one-to-one model.
- Use only the provided user unit for start, restart, and stop practice. Keep system-manager control commands out of the student lab.
- Explain active versus enabled with concrete status output; do not enable the practice unit.
- Use `systemctl get-default` and `journalctl -b -p err --no-pager` as read-only orientation commands; journal visibility varies by account and configuration.
- Explain that `systemctl reload` asks the application to reread its configuration, while `daemon-reload` rereads systemd unit definitions.
- Unit status may include recent logs, but use `journalctl` to teach deliberate filtering and time context.
- Journal access varies. Do not instruct learners to use `sudo` to read logs they are not authorized to access.
- Have students inspect the unit file before installing it and remove the exact user-level file during cleanup.
- The user manager and journal behavior vary across desktop sessions and distributions; provide a prepared transcript if the unit is unavailable.
- Ask students to redact usernames, hostnames, addresses, tokens, and other sensitive data from reports.

## Suggested slide outline

1. Hardware inventory, drivers, and kernel modules
2. `/dev`, sysfs, and udev
3. IRQ and DMA concepts
4. Processes, daemons, services, and units
5. Firmware, bootloader, kernel, initramfs, and systemd startup sequence
6. Common unit types: service, socket, target, timer, and mount
7. Active state versus enabled state
8. Start, stop, restart, reload, and daemon-reload
9. Unit-file sections and startup targets
10. Journal filtering with `journalctl`
11. A safe evidence-based service troubleshooting sequence
12. Boot troubleshooting: locate the stage before choosing a remedy

## Commands introduced

`lspci -nnk`, `lsusb -t`, `lsblk`, `lsmod`, `modinfo MODULE`, `udevadm info --query=all /dev/DEVICE`, `ls /sys/bus /sys/class /sys/block`, and `journalctl -k -b -n 30 --no-pager`; `systemctl get-default`, `systemctl --user status`, `list-units`, `is-active`, `is-enabled`, `start`, `restart`, `stop`, and `daemon-reload`; `journalctl -b`, `-p err`, `--user -u`, `-n`, `--since`, and `--no-pager`. State-changing actions are limited to the provided user unit; the lab does not enable it or alter system services.

## Instructor references

- [Linux kernel documentation: initramfs](https://www.kernel.org/doc/html/latest/filesystems/ramfs-rootfs-initramfs.html) — early userspace and transition to the real root filesystem.
- [GNU GRUB manual](https://www.gnu.org/software/grub/manual/grub/html_node/index.html) — bootloader concepts and configuration; firmware and installation details vary.
- [systemd bootup](https://www.freedesktop.org/software/systemd/man/latest/bootup.html) — startup sequence and targets managed by systemd.
- [lspci(8)](https://man7.org/linux/man-pages/man8/lspci.8.html) and [lsusb(8)](https://man7.org/linux/man-pages/man8/lsusb.8.html) — PCI/USB inventory and driver information.
- [udevadm](https://www.freedesktop.org/software/systemd/man/latest/udevadm.html) — querying device properties; trigger and control operations are outside the lab.
- [Linux kernel documentation: sysfs](https://www.kernel.org/doc/html/latest/filesystems/sysfs.html) — device, bus, and driver objects exposed through `/sys`.
- [Linux kernel documentation: DMA API](https://www.kernel.org/doc/html/latest/core-api/dma-api-howto.html) — kernel and driver coordination for DMA.
- [lsmod(8)](https://man7.org/linux/man-pages/man8/lsmod.8.html) and [modinfo(8)](https://man7.org/linux/man-pages/man8/modinfo.8.html) — inspect loaded kernel modules and module metadata.
- [systemctl](https://www.freedesktop.org/software/systemd/man/latest/systemctl.html) — unit inspection, lifecycle actions, enablement, and manager reload behavior.
- [systemd.unit](https://www.freedesktop.org/software/systemd/man/latest/systemd.unit.html) — unit structure, dependencies, ordering, and installation sections.
- [systemd.service](https://www.freedesktop.org/software/systemd/man/latest/systemd.service.html) — service unit configuration and process supervision.
- [journalctl](https://www.freedesktop.org/software/systemd/man/latest/journalctl.html) — journal access, boot selection, and filtering.
