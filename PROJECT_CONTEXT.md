# Linux Introduction Curriculum Project

## Project brief

Design and build a comprehensive introductory Linux curriculum consisting of 14 lessons. Each lesson should be developed independently while maintaining a natural progression toward practical Linux subject mastery. The project includes a rich narrative textbook, hands-on labs, instructor materials, assessments, glossaries, and Moodle question banks.

## Established course decisions

- The course contains 14 lessons.
- Lesson 1 is the prototype for the remaining lessons.
- Lesson 1 is designed for approximately 2–3 hours, including the installation lab.
- Ubuntu Desktop LTS is the default student environment.
- Ubuntu installation is a core part of Lesson 1.
- Each textbook chapter must use explanatory narrative prose rather than compressed outline notes.
- Each chapter includes its glossary as the final section of the textbook chapter.
- Each chapter has a Moodle XML question bank with at least 20 graded questions.
- Question banks use multiple-choice and true/false questions.
- Question banks must test both conceptual content and every Linux command introduced in the chapter.
- Future lesson materials should preserve consistent terminology, difficulty progression, command notation, and instructional style.

## Lesson 1 scope

Lesson 1 introduces:

- operating-system purpose and responsibilities;
- process, memory, file-system, and device management;
- GUI and CLI interfaces;
- Unix history and influence;
- the GNU Project;
- Linus Torvalds and the Linux kernel;
- open-source development and community collaboration;
- POSIX and portability concepts;
- Linux system layers;
- kernel subsystems, including system calls, process management, memory management, VFS, networking, device drivers, and architecture-dependent code;
- Linux distributions and distribution selection;
- package management, including APT, DNF, RPM, and Pacman;
- Ubuntu Desktop installation methods and storage safety;
- post-installation updates and system inspection.

## Commands introduced in Lesson 1

```bash
whoami
hostname
uname -srm
uname -a
lscpu
free -h
lsblk
mkdir -p ~/lesson1-demo/cli-created
cat /etc/os-release
sudo apt update
sudo apt upgrade -y
```

Commands should be explained in context, including their purpose, expected output, and any safety implications. Administrative commands such as `sudo apt upgrade -y` should not be presented as unexplained magic.

## Current artifacts

- Lessons 1–13 each have a Markdown lesson file and Moodle XML question bank.
- `course-progression.md` — The 14-lesson roadmap and scope for future modules.
- `README.md` — Repository purpose, current artifacts, and project development standards.

## Textbook chapter structure

Use this order unless a lesson requires a justified variation:

1. Lesson purpose and learning objectives
2. Narrative textbook chapter
3. Guided demonstrations
4. Student lab
5. Knowledge check and answer key
6. Chapter glossary
7. Instructor materials

The glossary belongs inside the textbook chapter, immediately before instructor materials.

## Moodle question-bank standard

Every chapter requires at least 20 graded questions in Moodle XML. Include a deliberate mix of:

- multiple-choice questions with one clearly correct answer;
- true/false questions that test meaningful distinctions;
- conceptual questions mapped to chapter objectives;
- command-recognition and command-purpose questions for every command introduced;
- feedback where it improves learning;
- safe wording that does not encourage destructive actions on personal systems.

## GitHub status

The workspace is `G:\NewCourseMaterial\Linux_Introduction`. The public repository `flybfree/Linux_Introduction_recovered` was created from this local copy. The `recovery` remote tracks that repository; `origin` still points to the original `flybfree/Linux_Introduction` repository. The local `main` branch currently tracks `recovery/main`.

## Recommended next steps

Continue with Lesson 14 — Integration Capstone — following the scope in `course-progression.md`.
