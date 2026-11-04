# Lesson 2 — Working with the Linux Shell

**Course position:** 2 of 14  
**Suggested duration:** 2–3 hours  
**Default environment:** Ubuntu Desktop LTS in an approved lab VM or computer  
**Assessment format:** Moodle XML question bank plus practical lab

## Lesson purpose

Lesson 1 introduced the operating system, the Linux kernel, the user environment, and the distinction between a graphical interface and a command-line interface. This lesson makes the command line practical. Students learn how a shell interprets commands, how to obtain reliable help, how Linux names locations, how commands receive input and produce output, and how small commands can be combined into useful pipelines.

The goal is not to memorize a long list of commands. The goal is to develop a repeatable habit: identify the task, read the command's documentation, construct a command carefully, inspect its output, and verify the result.

## Learning objectives

By the end of this lesson, students will be able to:

1. Differentiate between graphical user interfaces and command-line interfaces and explain appropriate use cases for each.
2. Define a shell and describe its role between the user, applications, and the Linux operating system.
3. Use `man` and `info` pages, command help options, and search tools to find command information.
4. Create, inspect, modify, and remove shell environment variables.
5. Create, inspect, and remove shell aliases.
6. Explain the difference between absolute paths, relative paths, directory names, and filenames.
7. Read and construct basic command syntax involving commands, options, arguments, quoting, and whitespace.
8. Redirect standard input, standard output, and standard error safely.
9. Combine commands with pipelines and explain how data moves through a pipeline.
10. Use `ps`, `top`, and `kill` to inspect and manage processes started in the lab.
11. Edit a shell configuration file with `nano` and apply changes with `source`.

## Instructor preparation

- Provide Ubuntu systems with Bash, the standard manual pages, GNU Info documentation, and `nano` installed.
- Ask students to work only in `~/lesson2-lab`; do not use the home directory as a practice area for destructive commands.
- Prepare a demonstration process that students can start and terminate themselves. `sleep 600` is sufficient.
- Explain that `kill` sends a signal; it does not always mean immediate or forceful termination.
- Decide whether students may edit their real `~/.bashrc`. For a first lab, a temporary configuration file or a clearly marked block in `~/.bashrc` is safer. Students should make a backup before changing a persistent configuration file.
- Confirm that students understand that `sudo` is not needed for this lesson.

## Suggested lesson sequence

| Time | Activity |
|---:|---|
| 10 min | GUI and CLI comparison; connect to Lesson 1 |
| 20 min | Shell overview and command interpretation |
| 20 min | Getting help with `man`, `info`, and `--help` |
| 20 min | Paths, filenames, command structure, and quoting |
| 20 min | Variables, aliases, `nano`, and `source` |
| 20 min | Input/output, error redirection, and pipelines |
| 20 min | Process inspection and safe process control |
| 35–45 min | Student practice lab |
| 10 min | Knowledge check and exit ticket |

---

# Textbook chapter

## 1. User interfaces in Linux

A user interface is a way for a person to communicate with a computer. In Lesson 1, we described two broad interface styles: the graphical user interface (GUI) and the command-line interface (CLI). Both interfaces ultimately ask the operating system to provide services. They differ in how the request is expressed and how the result is presented.

A GUI uses windows, menus, icons, buttons, and pointer or touch input. Ubuntu's GNOME desktop is a GUI. KDE Plasma, Xfce, LXQt, and Cinnamon are other desktop environments commonly used with Linux. GNOME emphasizes a streamlined workflow, KDE Plasma offers extensive customization, Xfce favors simplicity and low resource use, LXQt targets lightweight systems, and Cinnamon provides a familiar traditional desktop layout. A GUI is useful when a task is visual, when direct manipulation is convenient, or when a new user benefits from discovering available actions through menus. Opening a document, arranging windows, viewing photographs, and changing a display setting are often comfortable GUI tasks.

A CLI accepts typed text, usually through a terminal emulator running a shell. A CLI is useful when commands need to be precise, repeatable, recordable, or automated. Developers and administrators use command lines to inspect systems, process large numbers of files, connect to remote machines, and perform the same operation consistently. A command can also be placed in a script and run later.

The GUI and CLI are not competing operating systems. A graphical file manager and a shell may request the same filesystem services from the kernel. The difference is the layer that helps the user construct the request. Linux professionals normally use both interfaces and choose based on the task.

## 2. The shell: an interpreter for commands

A **shell** is a program that reads commands, interprets shell syntax, starts other programs, and reports results. Bash, the Bourne Again Shell, is the default shell in many Ubuntu installations, although Zsh, Fish, and Dash are also common shells. A shell can be interactive, accepting commands as a person types them, or non-interactive, executing commands from a script. A terminal emulator provides the window and keyboard interaction; the shell runs inside that terminal. The terminal is not the shell, and the shell is not the kernel.

When a user types a command, several things happen. The shell reads the line, recognizes special characters such as pipes or redirection operators, expands variables and certain filename patterns, locates the command, and starts the appropriate program. That program may request services from libraries and the kernel. The kernel manages protected operations such as creating a process, opening a file, or communicating with a device. The result travels back through the program and shell to the terminal.

Some commands are separate executable programs. Other commands are **shell builtins**, which are implemented inside the shell because they need to change the shell's own state. `cd`, `alias`, `export`, `unset`, and `source` are examples of builtins. Running `cd` as a separate child program would not change the parent shell's current directory, so the shell must implement it itself.

The prompt often displays useful context. A prompt such as `student@ubuntu:~$` may show the username, hostname, current directory, and an indication that the user is not the administrator. Prompt appearance varies by configuration, so students should not treat one exact prompt as universal.

## 3. Getting help

Linux commands are designed to be read with documentation nearby. A reliable Linux user does not guess at unfamiliar options, especially when a command can modify or delete data.

### Command help options

Many programs provide a short summary with `--help`:

```bash
ls --help
printf --help
```

The output is useful for a quick reminder, but it may be less complete than a manual page. Some commands use `-h` or another option for help, so read the command's own documentation rather than assuming every program behaves identically.

### Manual pages with `man`

The `man` command opens a manual page:

```bash
man ls
man bash
```

Manual pages are organized into sections. Section 1 generally contains user commands, while other sections cover system calls, library functions, device files, configuration formats, and administration topics. If a name exists in more than one section, specify the section number, such as `man 5 passwd` for the password-file format or `man 1 passwd` for the user command.

Inside a manual page, use the arrow keys or Page Up and Page Down to move. Press `/` followed by a search term and Enter to search. Press `n` to find the next match and `q` to quit. The `NAME`, `SYNOPSIS`, `DESCRIPTION`, `OPTIONS`, and `EXAMPLES` sections are especially useful. A synopsis is a notation guide, not a command to copy literally; square brackets usually indicate optional material.

### Info pages with `info`

Some GNU tools provide more extensive, linked documentation in Info format:

```bash
info coreutils
info bash
```

Info pages are organized as a navigable document. Follow links with Enter, move among nodes with the arrow keys or Page Up and Page Down, and press `q` to quit. Not every command has a useful Info page, and not every manual page has an equivalent Info document. Use the source that best explains the command you need.

Other useful discovery commands include `apropos keyword`, which searches manual-page descriptions, and `whatis command`, which gives a short description when one is available. These tools help when the user knows the task but not the command name.

## 4. Paths and filenames

Linux organizes files in a hierarchical directory tree. A **path** describes a location in that tree. The root directory is written `/`. A user's home directory can be represented by `~`, and the current directory can be represented by `.`. The parent of the current directory is `..`.

An **absolute path** begins at the root directory and identifies a location independently of the current directory. For example:

```text
/home/student/lesson2-lab/notes.txt
```

A **relative path** begins from the current directory. If the current directory is `/home/student`, the relative path `lesson2-lab/notes.txt` refers to the same file. Relative paths are shorter and convenient within a project; absolute paths are unambiguous when a command may run from an unknown location.

A filename is one component of a path, while a directory name identifies a container in the tree. Linux permits spaces and many punctuation characters in filenames, but unusual names require careful quoting or escaping. Names beginning with `.` are hidden by many directory listings, but they are not inherently more secure.

The shell expands `~` and variables before starting a command. Quoting controls that interpretation. Single quotes preserve the characters inside them literally, while double quotes still allow variable and command expansion but preserve spaces. When in doubt, quote a path:

```bash
printf '%s\n' "$HOME/lesson2-lab"
```

## 5. Basic command structure and syntax

A simple command commonly has this structure:

```text
command [options] [arguments]
```

The command identifies a program or builtin. Options modify behavior; they may use short forms such as `-l` or longer forms such as `--all`. Arguments provide the objects or values on which the command operates. Whitespace separates words unless quoting groups them.

For example:

```bash
ls -l "$HOME/lesson2-lab"
```

Here `ls` is the command, `-l` is an option, and the quoted path is an argument. The option and argument are separate ideas: an option changes how the program behaves, while an argument identifies what it should process.

The shell also recognizes control operators. A semicolon runs commands sequentially regardless of the first command's result. `&&` runs the next command only when the first succeeds, while `||` runs the next command when the first fails. These operators are useful but should be introduced deliberately; students should inspect commands before combining them.

Most commands return an **exit status**. Zero conventionally means success, and a nonzero value indicates some kind of failure or exceptional condition. The special parameter `$?` contains the most recent command's exit status until another command changes it.

## 6. Environment variables and aliases

An **environment variable** is a named value available to a shell and, when exported, to programs started by that shell. Common variables include `HOME`, `PATH`, `PWD`, `SHELL`, and `USER`. Display a variable by prefixing its name with `$`:

```bash
printf 'Home: %s\n' "$HOME"
printf 'Path: %s\n' "$PATH"
```

Create or change a shell variable with an assignment that has no spaces around `=`:

```bash
COURSE=linux
printf '%s\n' "$COURSE"
```

This variable exists in the current shell. To make it part of the environment inherited by programs started from that shell, export it:

```bash
export COURSE
```

The combined form `export COURSE=linux` both assigns and exports the variable. `unset COURSE` removes it from the current shell. A child process receives a copy of exported variables; changing a variable in the child does not change the parent shell.

The `PATH` variable is a colon-separated list of directories that the shell searches for executable commands. Do not replace `PATH` casually. If you need to add a personal directory, preserve the existing value, for example:

```bash
export PATH="$HOME/bin:$PATH"
```

An **alias** assigns a short name to a command string:

```bash
alias ll='ls -lah'
alias
```

Aliases make frequent interactive commands easier to remember, but they can hide behavior from someone reading a command or script. An alias exists only in the current shell unless it is defined in a startup file. Remove it with `unalias ll`.

## 7. Editing configuration and applying changes

Bash reads startup files according to how it is started and configured. `~/.bashrc` commonly contains interactive-shell settings such as aliases and prompt configuration. Because a change to this file affects future shells, make a backup before editing it:

```bash
cp ~/.bashrc ~/.bashrc.lesson2-backup
```

Use `nano` to edit a file:

```bash
nano ~/.bashrc
```

In Nano, type normally, use the arrow keys to move, press `Ctrl+O` to write the file, press Enter to confirm the filename, and press `Ctrl+X` to exit. If you are editing a real startup file, add a clearly labeled block and avoid changing unrelated lines.

The `source` builtin reads commands from a file in the current shell:

```bash
source ~/.bashrc
```

The shorter equivalent is `. ~/.bashrc`. Sourcing applies assignments and aliases to the current shell without opening a new terminal. A syntax error or unintended command in a sourced file runs in the current shell, so review the file first. For the student lab, a temporary file can be sourced safely:

```bash
printf '%s\n' "alias lesson='printf Lesson-2'" > "$HOME/lesson2-lab/lesson2-shell-setup"
source "$HOME/lesson2-lab/lesson2-shell-setup"
```

Other Bash startup files have different roles. `~/.bashrc` is commonly read for interactive non-login shells. `~/.bash_profile` or, on many Ubuntu installations, `~/.profile` may be read for login sessions and may in turn load `.bashrc`. A separate `~/.bash_aliases` file is often used to keep aliases organized. The exact startup sequence depends on how Bash is launched, so inspect the relevant documentation before assuming that a change will apply to every session.

The `date` command displays the system's current date and time:

```bash
date
```

Displaying the time is safe. Setting the system clock is an administrative operation and is outside this lesson; students should not attempt to change system time on a shared or personal computer.

## 8. Standard input, output, and error

Linux programs commonly use three standard streams. **Standard input** is file descriptor 0 and is the normal source of input. **Standard output** is descriptor 1 and is the normal destination for results. **Standard error** is descriptor 2 and is used for diagnostic messages. A terminal normally connects all three to the screen or keyboard, but the shell can redirect them.

Terminals are represented through device files. `/dev/tty` refers to the controlling terminal for the current process. `/dev/pts/` contains pseudo-terminals commonly created for terminal-emulator windows and remote sessions. These device files help explain why a terminal can be treated as an input or output destination, but students should not write to arbitrary device files. Use ordinary files in the lab for redirection.

The `>` operator sends standard output to a file, replacing the file if it already exists. The `>>` operator appends instead. Use these only with a path you have verified:

```bash
printf 'first line\n' > "$HOME/lesson2-lab/output.txt"
printf 'second line\n' >> "$HOME/lesson2-lab/output.txt"
```

The `<` operator supplies a file as standard input:

```bash
wc -l < "$HOME/lesson2-lab/output.txt"
```

Redirect standard error with `2>` or append it with `2>>`:

```bash
ls "$HOME/lesson2-lab/missing-file" 2> "$HOME/lesson2-lab/error.txt"
```

To redirect both standard output and standard error in Bash, use `> file 2>&1` or the Bash shorthand `&> file`. Be cautious: hiding errors can make troubleshooting harder. During practice, separate output and error files so students can see the distinction.

## 9. Pipelines

A **pipeline** connects the standard output of one command to the standard input of another using `|`:

```bash
ls -la "$HOME/lesson2-lab" | wc -l
```

The first command does not write directly to a file in this example. Its output becomes input for `wc`. Pipelines allow small programs to cooperate. For example:

```bash
printf '%s\n' "$PATH" | tr ':' '\n' | sort
```

This command prints the directories in `PATH`, changes colons to newlines, and sorts the resulting lines. Each stage has a clear role. Build pipelines one stage at a time and inspect intermediate output when a result is surprising.

The shell normally starts each pipeline component as a process. This connects the lesson's shell concepts to process management: a pipeline is not a single magical operation, but a group of cooperating processes connected by streams.

## 10. Processes and safe process control

A process is a running instance of a program. In Lesson 1, we described process management as a kernel responsibility. In this lesson, students observe that responsibility from the shell.

The `ps` command provides a snapshot of processes. `ps` with no options commonly shows processes associated with the current terminal. `ps -f` adds more detail, and `ps aux` is a widely used view of processes on Linux systems. Options vary between command styles, so consult `man ps`.

The `top` program provides a live, updating view of processes and resource use:

```bash
top
```

Press `q` to quit. Do not terminate a process merely because its name is unfamiliar; first determine what it is and whether it belongs to your lab activity.

Start a harmless process in the foreground:

```bash
sleep 600
```

Press `Ctrl+C` to send an interrupt and return to the shell. Alternatively, start it in the background:

```bash
sleep 600 &
```

The shell displays a job number and process ID. Find the process with `ps` or `pgrep`, then send a normal termination request:

```bash
kill PID
```

Replace `PID` with the process ID shown for the process you started. `kill` sends `SIGTERM` by default, giving the program an opportunity to clean up. A forceful signal such as `kill -9` should not be a first choice and should never be used on an unknown system process during this lab.

Processes form a hierarchy. A parent process starts child processes, and the shell commonly becomes the parent of programs launched from it. The process ID (PID), parent process ID (PPID), owner, command, and state help administrators understand what is running and how it was started.

---

# Guided demonstrations

## Demonstration 1: GUI and CLI accomplish the same kind of work

1. Open the file manager and create `lesson2-demo` in the home directory.
2. Open a terminal and run `mkdir -p "$HOME/lesson2-demo/cli-created"`.
3. Refresh the file manager and show the directory created by the CLI.
4. Explain that the file manager and `mkdir` use different interfaces to request filesystem services.

## Demonstration 2: Read documentation before using an option

```bash
man printf
```

Show the `SYNOPSIS`, search for `FORMAT`, and quit with `q`. Then run:

```bash
printf --help
```

Ask students to identify what the short help output includes and what the manual page explains in greater detail.

## Demonstration 3: Variables, aliases, and scope

```bash
DEMO_VALUE='shell only'
export DEMO_EXPORTED='child can read this'
printf '%s\n' "$DEMO_VALUE" "$DEMO_EXPORTED"
bash -c 'printf "child: %s / %s\\n" "$DEMO_VALUE" "$DEMO_EXPORTED"'
unset DEMO_VALUE DEMO_EXPORTED
alias ll='ls -lah'
ll "$HOME/lesson2-lab"
unalias ll
```

Explain why the child can read the exported variable but not the unexported variable.

## Demonstration 4: Redirection and a pipeline

```bash
printf '%s\n' zeta alpha beta alpha > "$HOME/lesson2-lab/words.txt"
sort "$HOME/lesson2-lab/words.txt" | uniq -c
wc -l < "$HOME/lesson2-lab/words.txt"
```

Show the difference between writing output to a file with `>` and sending output to another process with `|`.

## Demonstration 5: Inspect and terminate a lab process

```bash
sleep 600 &
ps -f --pid $!
kill $!
```

Explain `$!` as the PID of the most recently started background process. Confirm that the command targets only the process just created by the demonstration.

---

# Student lab: Lesson 2 — Working with the Linux Shell

## Safety and scope

Work only inside `~/lesson2-lab`. Do not use `sudo`, do not edit system files, and do not run a command copied from the internet unless the instructor has reviewed it. Before using `>`, `>>`, `rm`, `kill`, or `source`, verify the path, target, or file contents. If a command's effect is unclear, stop and ask.

## Part A: Prepare the practice area

```bash
mkdir -p "$HOME/lesson2-lab"
cd "$HOME/lesson2-lab"
printf 'Linux\nShell\nPractice\n' > words.txt
pwd
ls -la
```

Record the output of `pwd`. Explain whether the path is absolute or relative.

## Part B: Explore help

1. Use `man ls` and locate the description of the `-l` option.
2. Use `man 5 passwd` and explain why this is different from `man passwd`.
3. Use `info coreutils` and locate the section describing `printf`.
4. Run `apropos directory` and identify one command you have not used before. Do not run it unless the instructor approves.
5. Use `ls --help` to find an option that displays hidden files.

## Part C: Paths and command syntax

1. From `~/lesson2-lab`, run `pwd` and `ls .`.
2. Run `ls ..` and explain what `..` means.
3. Use an absolute path and a relative path to display `words.txt` with `cat`.
4. Create a directory named `space name` with `mkdir 'space name'`.
5. List it using quotes, then remove the empty directory with `rmdir 'space name'`.
6. Use `echo` and `printf` to compare ordinary text output with formatted output.

## Part D: Variables and aliases

Run these commands and record what changes:

```bash
LAB="$HOME/lesson2-lab"
export LESSON_NUMBER=2
printf 'Lab: %s\nLesson: %s\n' "$LAB" "$LESSON_NUMBER"
alias lsl='ls -l'
lsl "$LAB"
unalias lsl
unset LESSON_NUMBER
```

Create a temporary setup file without editing the real `.bashrc`:

```bash
printf '%s\n' "export LESSON_MODE=practice" "alias lessonpwd='printf %s\\n \"$PWD\"'" > lesson2-shell-setup
source lesson2-shell-setup
printf '%s\n' "$LESSON_MODE"
lessonpwd
unset LESSON_MODE
unalias lessonpwd
```

If your instructor directs you to practice with `nano`, open `nano lesson2-shell-setup`, add a comment beginning with `#`, save with `Ctrl+O`, and exit with `Ctrl+X`. Review the file before sourcing it.

## Part E: Redirection and pipelines

1. Redirect the output of `printf '%s\n' one two three` to `numbers.txt`.
2. Append `four` without replacing the existing lines.
3. Count the lines with `wc -l < numbers.txt`.
4. Run `ls missing-file` and redirect only the error to `errors.txt`.
5. Display the sorted unique lines from `words.txt` with `sort words.txt | uniq`.
6. Count the entries in the `PATH` variable with `printf '%s' "$PATH" | tr ':' '\n' | wc -l`.

Inspect every file created by the lab. Explain which output came from standard output and which came from standard error.

## Part F: Processes

1. Run `ps -f` and identify the shell process.
2. Start `sleep 300 &` and record its PID.
3. Locate it with `ps -f --pid PID`, replacing `PID` with the value you recorded.
4. Open `top`, identify the shell, and quit with `q`.
5. Run `kill PID` and verify that the `sleep` process is gone.

Do not use `kill -9` unless the instructor explicitly demonstrates it on a disposable process and explains why normal termination was insufficient.

## Lab reflection

Answer in two or three sentences each:

1. Why is a shell different from a terminal emulator and from the kernel?
2. When is a GUI more suitable than a CLI? When is a CLI more suitable than a GUI?
3. What is the difference between a shell variable and an exported environment variable?
4. How does `>` differ from `>>`, and how do both differ from `|`?
5. Why should a student inspect a command's documentation before using an unfamiliar option?
6. Why is `kill -9` not the normal first step for stopping a process?

## Lab success criteria

The student can:

- locate useful information with `man`, `info`, and `--help`;
- explain and use absolute and relative paths;
- create and remove a variable and alias in the current shell;
- edit and source a temporary configuration file;
- redirect output and errors and construct a pipeline; and
- inspect and safely terminate a process started in the lab.

---

# Knowledge check

1. What is a shell?
2. How does a CLI differ from a GUI?
3. What does `man` provide?
4. What is the purpose of `info`?
5. What is the difference between an absolute path and a relative path?
6. In `ls -l /tmp`, identify the command, option, and argument.
7. What does `export` do to a variable?
8. What does `alias` create?
9. What does `>` do, and why can it be dangerous?
10. What is the purpose of the pipe operator `|`?
11. What is a process ID?
12. What signal does `kill PID` send by default?

## Answer key

1. A program that interprets commands, expands shell syntax, starts programs, and reports results.
2. A CLI uses typed commands; a GUI uses graphical controls. Both can request services from the same operating system.
3. It opens a manual page containing command reference information.
4. It provides linked GNU documentation for some commands and tool collections.
5. An absolute path begins at `/`; a relative path is interpreted from the current directory.
6. `ls` is the command, `-l` is the option, and `/tmp` is the argument.
7. It places the variable in the environment inherited by programs started from that shell.
8. It creates a shortcut that maps a name to a command string in the current shell.
9. It redirects standard output to a file, replacing existing contents; an incorrect path can overwrite data.
10. It connects one command's standard output to another command's standard input.
11. A process ID is the numeric identifier assigned to a running process.
12. `kill PID` sends `SIGTERM` by default, requesting normal termination.

## Exit ticket

Complete this sentence:

> “When I am unsure what a Linux command will do, I will first ______ because ______.”

---

# Chapter glossary

**Absolute path:** A path beginning at the root directory `/`.  
**Alias:** A shell-defined shortcut for a command string.  
**Argument:** A value or object supplied to a command.  
**CLI:** Command-line interface; an interface operated by typed commands.  
**Environment variable:** A named value exported to programs started by a shell.  
**Exit status:** A numeric result returned by a command, conventionally zero for success.  
**GUI:** Graphical user interface.  
**Option:** A command-line setting that changes a program's behavior.  
**Path:** A description of a file or directory's location in the filesystem tree.  
**Pipeline:** A connection in which one command's standard output becomes another command's standard input.  
**Process ID (PID):** The numeric identifier assigned to a running process.  
**Redirection:** Shell syntax that changes where a command reads input or writes output.  
**Shell:** A command interpreter that provides a text-based user environment.  
**Standard error:** A process stream conventionally used for diagnostic messages.  
**Standard input:** A process stream conventionally used for input.  
**Standard output:** A process stream conventionally used for normal results.  

---

# Instructor materials

## Instructor notes

- Keep returning to the boundary between terminal emulator, shell, user-space program, and kernel.
- Have students read a small part of a manual page during class instead of treating documentation as a lecture topic only.
- Demonstrate redirection with a disposable file and emphasize that `>` replaces content.
- Use `$!` only for a process the instructor or student just started; do not teach students to kill arbitrary PIDs.
- Avoid making `.bashrc` editing a prerequisite. A temporary setup file teaches `source` with less risk.
- If `info` is unavailable in a minimal image, provide the `info` package or use a prebuilt VM. Do not silently omit the objective.
- GUI examples should remain concrete but brief; the lesson's main practice is shell reasoning.

## Suggested slide outline

1. Lesson goals and lab safety
2. GUI and CLI: two ways to request services
3. What a shell does
4. Command interpretation and the kernel boundary
5. Finding help with `man`, `info`, and `--help`
6. Paths, filenames, and quoting
7. Command structure and exit status
8. Environment variables and aliases
9. `nano`, startup files, and `source`
10. Standard input, output, and error
11. Redirection and pipelines
12. Processes, PIDs, `ps`, `top`, and `kill`
13. Practice lab and reflection

## Commands introduced

`pwd`, `ls`, `cd`, `mkdir`, `rmdir`, `cat`, `printf`, `echo`, `date`, `man`, `info`, `apropos`, `whatis`, `clear`, `history`, `export`, `unset`, `alias`, `unalias`, `nano`, `source`, `cp`, `sort`, `uniq`, `wc`, `tr`, `ps`, `top`, `sleep`, and `kill`.
