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

Each file defines a class based on `nebula.plugins.CLIPlugin`. Its `name` is the command name, and the class docstring is the description shown in the command list.

If two plugins have the same `name`, the first one found is used and a warning is printed. A site command with the name of a built-in command replaces it, also with a warning.

### A simple command

The plugin has an `async def main` method. Its parameters are the command's arguments, passed as `key=value`:

```python
import nebula


class Cleanup(nebula.plugins.CLIPlugin):
    """Remove old temporary assets."""

    name = "cleanup"

    async def main(self, days=30, dry=False):
        ...
```

```bash
docker compose exec backend python -m cli cleanup days=7 dry=true
```

* `--days=7` works the same as `days=7`.
* A value is converted to the type of the parameter's default: `days` arrives as the integer `7`, `dry` as `True`. Booleans accept `true`, `false`, `1`, `0`, `yes` and `no`. A parameter without a default, or with `None`, receives the text as written.
* An argument the method doesn't have is an error that lists the available ones. A method with `**kwargs` accepts any argument.

### A command with sub-commands

For sub-commands, flags and positional arguments, give the plugin an `app` attribute holding a [Typer](https://typer.tiangolo.com/) application. It is used instead of `main`:

```python
import typer

import nebula
from cli.run import run_async

app = typer.Typer()


@app.command()
@run_async
async def export(channel: int, days: int = 7, verbose: bool = False):
    """Export the schedule of a channel."""
    ...


@app.command()
def formats():
    """List the export formats."""
    ...


class Report(nebula.plugins.CLIPlugin):
    """Schedule reports."""

    name = "report"
    app = app
```

```bash
docker compose exec backend python -m cli report export 1 --days 14 --verbose
docker compose exec backend python -m cli report formats
```

Typer commands are ordinary functions. Put `@run_async` under `@app.command()` for a command that is `async def`: it loads the Nebula settings and runs the coroutine.

!!! note "Existing plugins"
    Plugins written for earlier versions keep working. Two things changed: values are converted to the type of the parameter's default (they used to arrive as text), and a word that isn't `key=value` is now an error (it used to be ignored).
