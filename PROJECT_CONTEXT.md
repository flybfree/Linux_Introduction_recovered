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

## Graphics organization

- Course graphics and associated information live in `graphics/`, with a shared [index](graphics/README.md).
- Use `graphics/lesson-NN/` for each chapter as graphics are developed.
- Each lesson folder includes images, a README with captions, alt text, placement, and review status, plus generation prompts or editable sources.
- Chapters embed figures using relative paths. Chapter 1 includes Figure 1.1 (OS responsibilities), Figure 1.2 (Linux system structure), Figure 1.3 (distribution components), and Figure 1.4 (Unix/GNU/Linux history), with captions, accessibility text, review notes, and prompts in its graphics folder.

- Lesson 2 includes Figures 2.1–2.3: absolute and relative paths, command anatomy, and streams/redirection/pipelines. Supporting information and prompts live in `graphics/lesson-02/`.

- Lesson 3 includes Figures 3.1–3.3: filesystem locations, copy/move/rename behavior, and copies versus hard and symbolic links. Supporting information and prompts live in `graphics/lesson-03/`.

- Lesson 4 includes Figures 4.1–4.3: ownership and mode bits, file versus directory permissions, and umask creation filtering. Supporting information and prompts live in `graphics/lesson-04/`.

- Lesson 5 includes Figures 5.1–5.3: program/process/job identities, niceness, and interactive job-control transitions. Supporting information and prompts live in `graphics/lesson-05/`.

- Lesson 6 includes Figures 6.1–6.3: repositories/APT/dpkg, index refresh versus package upgrade, and shared-library dependencies. Supporting information and prompts live in `graphics/lesson-06/`.

- Lesson 7 includes Figures 7.1–7.3: script checks and launch methods, cron fields, and the lab report control/output flow. Supporting information and prompts live in `graphics/lesson-07/`.

- Lesson 8 includes Figures 8.1–8.3: sort/uniq adjacency, field selection and CSV limits, and the error-report pipeline. Supporting information and prompts live in `graphics/lesson-08/`.

- Lesson 9 includes Figures 9.1–9.4: storage/LVM layers, df versus du, backup restore chains, and archive restoration/verification. Supporting information and prompts live in `graphics/lesson-09/`.

- Lesson 10 includes Figures 10.1–10.3: TCP/IP layers, local versus gateway routing, and evidence-based network troubleshooting. Supporting information and prompts live in `graphics/lesson-10/`.

## Current artifacts

- Lessons 1–14 each have a Markdown lesson file. All Moodle XML question banks are stored in `Question Banks/`, using the existing `lesson-NN-moodle-question-bank.xml` names.
- `course-progression.md` — The 14-lesson roadmap and scope for future modules.
- `book-alignment-linux-plus-xk0-005.md` — mapping of course lessons to the published chapters of the CompTIA Linux+ XK0-005 All-in-One Exam Guide, Second Edition.
- `lesson-plans.md` — all 14 course lesson plans in the instructor's Objectives/Lecture Topics/Labs/Preparation/Assignments template, aligned to the companion textbook.
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

The 14-lesson sequence is drafted; review the full curriculum for consistency, then refine or prepare course delivery materials as needed.
