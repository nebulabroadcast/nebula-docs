# Command Line Tools

The backend has a command line interface for maintenance tasks. It runs inside the backend container and offers two kinds of commands: **built-in** ones that ship with Nebula, and **site commands**, which are your own CLI plugins.

---

## Running Commands

```bash
docker compose exec backend python -m cli              # list all commands
docker compose exec backend python -m cli <command>    # run one
docker compose exec backend python -m cli <command> --help
```

Without a command, the list of everything available is printed: built-in and site commands together, each with a one-line description.

A command that fails exits with status `1`. A wrong command name or wrong arguments exit with status `2` and say what was expected.

---

## Built-in Commands

| Command | What it does |
|---|---|
| `adduser` | Creates a user. |
| `version` | Prints the Nebula version. |

### adduser

```bash
docker compose exec backend python -m cli adduser
docker compose exec backend python -m cli adduser jane --email jane@example.com --no-admin
```

Without arguments the command asks for the login, an optional e-mail, whether the user is an administrator, and the password (typed twice, not shown). Anything given on the command line isn't asked for:

| Argument | Meaning |
|---|---|
| `login` | Login name. |
| `--email` | E-mail address. |
| `--full-name` | Full name. |
| `--admin` / `--no-admin` | Whether the user has administrator rights. |
| `--password` | The password. Prefer the prompt: a password on the command line ends up in the shell history. |

The password must have at least 8 characters, and the login must not be taken. A user who isn't an administrator has no permissions until an administrator grants some in the web interface.

---

## Site Commands

Site commands are Python files in the `cli/` subdirectory of the backend's plugin directory (`/plugins` in the backend container, configurable with the `NEBULA_PLUGIN_DIR` environment variable):

```text
plugins/
└── cli/
    ├── cleanup.py
    └── report.py
```

Each file defines a class based on `nebula.plugins.CLIPlugin`. Its `name` is the command name, and the class docstring is the description shown in the command list. The built-in commands are written the same way.

If two plugins have the same `name`, the first one found is used and a warning is printed. A site command with the name of a built-in command replaces it, also with a warning.

### A command

The plugin has an `async def main` method. Its parameters are the command's arguments:

```python
import nebula


class Cleanup(nebula.plugins.CLIPlugin):
    """Remove old temporary assets."""

    name = "cleanup"

    async def main(self, folder: int, days: int = 30, dry: bool = False):
        ...
```

```bash
docker compose exec backend python -m cli cleanup 5 --days 7 --dry
```

* A parameter **without a default** is a positional argument and must be given (`folder`).
* A parameter **with a default** is an option: `--days 7`. A boolean is a flag: `--dry`, and `--no-dry` to turn it off.
* Type hints give the types. A parameter without a type hint takes the type of its default; without either, it is text.
* An underscore in a parameter name is a dash on the command line: `retry_failed` is `--retry-failed`.
* The method's docstring, or the class's, is shown by `--help`, together with the arguments.
* The settings are loaded before `main` runs, so `nebula.settings` and `nebula.db` are ready to use.

Arguments can also be written as `key=value`, the form older plugins were called with:

```bash
docker compose exec backend python -m cli cleanup folder=5 days=7 dry=true
```

Booleans accept `true`, `false`, `1`, `0`, `yes` and `no`. A `main` with `**kwargs` receives every `key=value` word it has no parameter for, as text.

### A command with sub-commands

A plugin **without** `main` is a group. Every public `async def` method of the class is a sub-command, with arguments by the same rules:

```python
import nebula


class Report(nebula.plugins.CLIPlugin):
    """Schedule reports."""

    name = "report"

    async def export(self, channel: int, days: int = 7, verbose: bool = False):
        """Export the schedule of a channel."""
        ...

    async def formats(self):
        """List the export formats."""
        ...
```

```bash
docker compose exec backend python -m cli report
docker compose exec backend python -m cli report export 1 --days 14 --verbose
docker compose exec backend python -m cli report formats
```

Methods whose names start with an underscore, and methods that aren't `async def`, are not commands: use them as helpers.

### Help texts and choices

The command line is built with [Typer](https://typer.tiangolo.com/). A plugin doesn't need to import it, but can, to describe an argument further:

```python
from typing import Annotated

import typer


async def main(
    self,
    days: Annotated[int, typer.Option(help="Age of the assets to remove.")] = 30,
):
    ...
```

!!! note "Existing plugins"
    Plugins written for earlier versions keep working. Two things changed: values are converted to the type of the parameter's default (they used to arrive as text), and a word that isn't one of the command's arguments is now an error (it used to be ignored).
