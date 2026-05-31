# PP6

## Goal

In this exercise you will:

* Learn how to produce text output to the terminal in four different environments:

  1. **Bash** shell scripting
  2. **GNU Assembler** (GAS) using system calls
  3. **C** via the standard library
  4. **Python 3** using built‑in printing functions

**Important:** Start a stopwatch when you begin and work uninterruptedly for **90 minutes**. When time is up, stop immediately and record exactly where you paused.

---

> **⚠️ WARNING:** The **workflow** steps have been **updated** for PP6. Please review the new workflow below carefully before proceeding.

## Workflow

1. **Fork** this repository on GitHub
2. **Clone** your fork to your local machine
3. Create a **solutions** directory at the repository root: `mkdir solutions`
4. For each task, add your solution file into `./solutions/` (e.g., `[solutions/print.sh](./solutions/print.sh)`)
5. **Commit** your changes locally, then **push** to GitHub
6. **Submit** your GitHub repository link for review

---

## Prerequisites

* Several starter repos are available here:
  [https://github.com/orgs/STEMgraph/repositories?q=SSH%3A](https://github.com/orgs/STEMgraph/repositories?q=SSH%3A)

---

> **Note:** Place all your solution files under the `solutions/` directory in your cloned repo.

## Tasks

### Task 1: Bash Printing & Shell Prompt

**Objective:** Use both `echo` and `printf` to format and display text and variables, and customize your shell prompt and login/logout messages.

1. In `./solutions/`, create a file named `print.sh` based on the template below.
2. At the top of `print.sh`, add the shebang:

   ```bash
   #!/usr/bin/env bash
   ```
3. Implement at least three functions:

   * `print_greeting`: prints `Hello from Bash!` using `echo`.
   * `print_vars`: declares two variables (e.g., `name="Bash"`, `version=5.1") and prints them with `printf\` using format specifiers.
   * `print_escape`: demonstrates escape sequences: newline (`\n`), tab (`\t`), and ANSI color codes (e.g., `\e[32m` for green).
4. Make the script executable and verify it runs:

   ```bash
   chmod +x solutions/print.sh
   ./solutions/print.sh
   ```
5. Modify your **`~/.bashrc`** on your local machine to:

   * Change the `PS1` prompt to include your username and current directory (e.g., `export PS1="[\u@\h \W]\$ "`).
   * Display a **welcome** message on login (e.g., `echo "Welcome, $(whoami)!"`).
   * Display a **goodbye** message on logout (add a `trap` for `EXIT` to echo `Goodbye!`).
6. Reload your shell (`source ~/.bashrc`) and demonstrate the prompt, greeting, and exit message.

**Template for `solutions/print.sh`**

```bash
#!/usr/bin/env bash

print_greeting() {
    # TODO: echo "Hello from Bash!"
}

print_vars() {
    local name="Bash"
    local version=5.1
    # TODO: printf "Using %s version %.1f\n" "$name" "$version"
}

print_escape() {
    # TODO: printf "This is a newline:\nThis is a tab:\tDone!\n"
    # TODO: printf "\e[32mGreen text\e[0m and normal text\n"
}

# Call your functions:
print_greeting
print_vars
print_escape
```

**Solution Reference**
Place your completed `print.sh` in `solutions/` and commit. Then link it here:

```
[print.sh](https://github.com/YOUR_USERNAME/REPO_NAME/blob/main/solutions/print.sh)
```

#### Reflection Questions

1. **What is the difference between `printf` and `echo` in Bash?**
   - `echo` gibt Text einfach aus und ist schnell für unkomplizierte Ausgaben
   - `printf` erlaubt genaue Formatierung mit Platzhaltern wie `%s` oder `%d` und ist dadurch flexibler

2. **What is the role of `~/.bashrc` in your shell environment?**
   - `~/.bashrc` wird jedes Mal ausgeführt, wenn eine neue interaktive Bash-Shell gestartet wird, und setzt so deine persönliche Umgebung
   - Dort kann man z. B. Aliase, Variablen, den Prompt (`PS1`) oder Startbefehle wie Begrüßungsnachrichten definieren

3. **Explain the difference between sourcing (`source ~/.bashrc`) and executing (`./print.sh`).**
   - `source ~/.bashrc`** führt die Datei im *aktuellen Shell-Prozess* aus, dadurch bleiben Änderungen (z. B. Variablen oder Prompt) direkt aktiv.
   - `./print.sh`** startet ein *neues, separates Programm (Subshell)*, Änderungen darin beeinflussen die aktuelle Shell nicht.

---

### Task 2: GAS Printing (32‑bit Linux)

**Objective:** Use the Linux `sys_write` and `sys_exit` system calls in GAS to write text, while ensuring your repo only contains source files.

1. At the repository root, create a file named `.gitignore` that ignores:

   * Object files (`*.o`)
   * Binaries/executables (e.g., `print`, `print_c`)
   * Any editor or OS-specific files
     Commit this `.gitignore` file.
     **Explain:** Why should compiled artifacts and binaries not be committed to a Git repository?
2. In `./solutions/`, create a file named `print.s` using the template below.
3. Define a message in the `.data` section (e.g., `msg: .ascii "Hello from GAS!\n"`, `len = . - msg`).
4. In the `.text` section’s `_start` symbol, invoke `sys_write` (syscall 4) and then `sys_exit` (syscall 1) via `int $0x80`.
5. Assemble and link in 32‑bit mode:

   ```bash
   as --32 -o print.o solutions/print.s
   ld -m elf_i386 -o print print.o
   ```
6. Run `./print` and verify it outputs your message.

**Template for `solutions/print.s`**

```asm
    .section .data
msg:    .ascii "Hello from GAS!\n"
len = . - msg

    .section .text
    .global _start
_start:
    movl $4, %eax        # sys_write
    movl $1, %ebx        # stdout
    movl $msg, %ecx
    movl $len, %edx
    int $0x80

    movl $1, %eax        # sys_exit
    movl $0, %ebx        # status 0
    int $0x80
```

**Solution Reference**

```
[print.s](https://github.com/YOUR_USERNAME/REPO_NAME/blob/main/solutions/print.s)
```

#### Reflection Questions


1. **What is a file descriptor and how does the OS use it?**
   
Ein File Descriptor ist eine kleine Zahl, die vom Betriebssystem verwendet wird, um geöffnete Dateien oder andere I/O-Ressourcen (z. B. Dateien, Pipes, Sockets) zu identifizieren
Das Betriebssystem nutzt ihn, um Lese- und Schreibzugriffe den richtigen Ressourcen zuzuordnen (z. B. stdin, stdout, stderr)

2. **How can you obtain or duplicate a file descriptor for another resource (e.g., a file or socket)?**

Einen File Descriptor erhält man durch Systemaufrufe wie open() oder socket()
Duplizieren kann man ihn mit dup() oder dup2(), z. B. um Ein- oder Ausgabe umzuleiten

3. **What might happen if you use an invalid file descriptor in a syscall?**
   
Der Systemcall schlägt fehl und gibt einen Fehler zurück (z. B. EBADF)
Es können keine Daten gelesen oder geschrieben werden, da die Ressource nicht existiert oder bereits geschlossen ist

---

### Task 3: C Printing

**Objective:** Use `printf` and `puts` from `<stdio.h>` to display formatted data.

1. In `./solutions/`, create `print.c` based on the template below.
2. Implement `main()` to print a greeting, an integer, a float, and a string via `printf`, then use `puts`.
3. Compile and run:

   ```bash
   gcc -std=c11 -Wall -o print_c solutions/print.c
   ./print_c
   ```

**Template for `solutions/print.c`**

```c
#include <stdio.h>

int main(void) {
    printf("Hello from C!\n");
    printf("Integer: %d, Float: %.2f, String: %s\n", 42, 3.14, "test");
    puts("This is puts output");
    return 0;
}
```

**Solution Reference**

```
[print.c](https://github.com/YOUR_USERNAME/REPO_NAME/blob/main/solutions/print.c)
```

#### Reflection Questions




1. **Use `objdump -d` on `print_c` to find the assembly instructions corresponding to your `printf` calls.**
Mit objdump -d print_c sieht man den disassemblierten Maschinencode des Programms
Die printf-Aufrufe erscheinen meist als call printf@plt (oder puts@plt), weil die Funktion über die C-Standardbibliothek dynamisch gelinkt wird

2. **Why is the syntax written differently from GAS assembly? Compare NASM vs. GAS notation.**
GAS (GNU Assembler) verwendet z. B. %eax, $1 und Source-Destination-Reihenfolge movl $1, %eax
NASM verwendet keine % oder $ und schreibt z. B. mov eax, 1
Unterschied entsteht durch verschiedene Assembler-Syntax-Designs (Intel vs. AT&T Syntax)

3. **How could you use `fprintf` to write output both to `stdout` and to a file instead? Provide example code.**
```c
int main(void) {
    FILE *fp = fopen("output.txt", "w");
    fprintf(stdout, "Output to terminal\n");
    fprintf(fp, "Output to file\n");
    fclose(fp);
    return 0;
}
```
---

### Task 4: Python 3 Printing

**Objective:** Use Python’s `print()` function with various parameters and f‑strings.

1. In `./solutions/`, create `print.py` using the template below.
2. Implement `main()` to print a greeting, multiple values with custom `sep`/`end`, and an f‑string expression.
3. Make it executable and run:

   ```bash
   chmod +x solutions/print.py
   ./solutions/print.py
   ```

**Template for `solutions/print.py`**

```python
#!/usr/bin/env python3

def main():
    print("Hello from Python3!")
    print("A", "B", "C", sep="-", end=".\n")
    value = 2 + 2
    print(f"Two plus two equals {value}")

if __name__ == "__main__":
    main()
```

**Solution Reference**

```
[print.py](https://github.com/YOUR_USERNAME/REPO_NAME/blob/main/solutions/print.py)
```

#### Reflection Questions

**1. Is Python’s `print` behavior closer to Bash, Assembly, or C? Explain.**

* Python’s `print()` ist am ehesten mit C vergleichbar
* Wie `printf` in C schreibt es formatierten Text auf die Standardausgabe und unterstützt mehrere Werte und Formatierung
* Im Gegensatz zu Bash oder Assembly ist es jedoch deutlich höher abstrahiert und übernimmt viele Konvertierungen automatisch

**2. Can you inspect a Python script’s binary with `objdump`? Why or why not?**

* Nein, ein Python-Skript kann man nicht sinnvoll mit `objdump` untersuchen
* `objdump` ist für kompilierte Binärdateien gedacht, die direkten Maschinencode enthalten
* Python-Dateien sind Quelltext und werden erst zur Laufzeit vom Interpreter ausgeführt

---

**Remember:** Stop working after **90 minutes** and document where you stopped.
