# Minishell

A small Unix shell written in **C** as part of the **42 Urduliz** curriculum. This team project explores how a shell turns user input into commands, connects processes through pipes, and manages redirections, environment variables, and signals.

✅ Completed and approved at 42.

## Features

- Interactive prompt displaying the current working directory, with command history through GNU Readline.
- Tokenization of words and operators, with single and double quote handling.
- A command tree representing executable commands, pipes, and redirections.
- External command execution using `PATH`, relative paths, or absolute paths.
- Pipelines with `|`.
- Input and output redirections: `<`, `>`, and `>>`.
- Here-documents with `<<`, including quoted delimiters.
- Environment variable expansion and the last exit status through `$?`.
- Builtins: `echo` (including `-n`), `cd`, `pwd`, `export`, `unset`, `env`, and `exit`.
- Interactive signal handling: `Ctrl+C` resets the prompt, `Ctrl+\\` is ignored at the prompt, and `Ctrl+D` exits on end of input.

Minishell implements a focused subset of shell behavior. It is not a complete Bash implementation; logical operators `&&` and `||`, parenthesized command groups, and wildcard expansion are outside the implemented command-tree design.

## Architecture

Input is checked and parsed into a tree. Variable expansion is applied while command arguments are built. Execution then dispatches according to each node type.

| Component | Responsibility | Main files |
| --- | --- | --- |
| Interactive loop | Display the prompt, read input, and retain history | `src/main.c` |
| Tokenizer | Identify words and operators while respecting quotes | `src/token.c`, `src/token_count.c`, `src/token_util.c` |
| Parser | Build command and redirection nodes and recursively connect pipelines | `src/parse.c`, `src/parse_pipe.c`, `src/parse_redi.c` |
| Expansion | Expand environment variables and `$?` according to quoting | `src/expand.c`, `src/expand2.c` |
| Executor | Dispatch `EXEC`, `PIPE`, and `REDIR` nodes | `src/runcmd.c`, `src/run_exec.c`, `src/run_pipe.c`, `src/run_redir.c` |
| Environment and builtins | Manage shell variables and built-in commands | `src/environ*.c`, `src/builtin_*.c`, `src/bultins.c` |
| Signals and heredocs | Handle interactive signals and here-document input | `src/signal.c`, `src/heredoc*.c` |

The executor uses Unix system calls such as `fork`, `execve`, `pipe`, `dup2`, and `waitpid`. Builtins that modify shell state are handled in the shell process when run as standalone commands.

## Build and Run

### Requirements

- Linux or a compatible Unix environment.
- GCC and Make.
- GNU Readline and ncurses development libraries.
- Git to clone the repository.

On Debian or Ubuntu, install the build dependencies:

```sh
sudo apt install build-essential libreadline-dev libncurses-dev
```

### Compilation

```sh
git clone https://github.com/Yuliano96/minishell.git
cd minishell
make
./minishell
```

The Makefile builds the included `libft` and links the shell with Readline and ncurses.

| Command | Action |
| --- | --- |
| `make` | Build the library and shell |
| `make clean` | Remove object files |
| `make fclean` | Remove object files and the shell executable |
| `make re` | Clean and rebuild |

The root `make fclean` keeps `libft/libft.a`; use `make -C libft fclean` to remove that archive too.

## Usage Examples

Run these commands inside Minishell:

```sh
echo "Hello from Minishell"
pwd
ls -l | wc -l
echo "first line" > output.txt
echo "second line" >> output.txt
cat < output.txt
export PROJECT=minishell
echo "$PROJECT"
echo '$PROJECT'
echo $?
cat << EOF
hello
EOF
exit
```

These examples demonstrate supported syntax; they are not an automated test suite.

## Team and Contributions

Developed by **Yuliano Pacileo (`ypacileo`)** and **Esteban Izaguirre (`esteizag`)** at 42 Urduliz.

- **Yuliano:** responsible for parsing, tokenization, and command-tree construction; also contributed to command-tree execution.
- **Esteban:** primarily responsible for variable expansion.

This describes our main areas of work rather than exclusive ownership of every function.

## What I Learned

- Turning shell input into tokens and a structured command tree.
- Representing and executing commands, pipelines, and redirections.
- Working with processes, file descriptors, and interprocess communication.
- Understanding how quoting affects variable expansion.
- Managing dynamically allocated structures and their cleanup.
- Integrating components and sharing responsibilities in a C team project.
