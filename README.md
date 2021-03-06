# Akamai CLI

Akamai CLI is an ever-growing CLI toolkit for working with Akamai's API from the command line.

## Goals

- Simplicity
- Feature-full
- Consistent UX

## Installation

Akamai CLI is itself a Go application, but may rely on sub-commands that can be written using any language.

The easiest way to install Akamai CLI is to download a [Release](https://github.com/akamai/cli/releases) for your platform.

If you want to compile it from source, you will need Go 1.8 or later installed:

1. Clone this repository:  
  `git clone https://github.com/akamai/cli.git`
2. Change to the clone directory:  
  `cd akamai-cli`
3. Install dependencies using [Glide](https://glide.sh):  
  `glide install`
4. Compile the binary:  
  `go build -o akamai .` or on windows `go build -o akamai.exe`
5. Move it to your `PATH`

## Usage

All commands start with the `akamai` binary, followed by a `sub-command` which correlates directly to another binary in your path starting with `akamai-` or `akamaiTitlecase` (in the case of node.js binaries).

### Built-in commands

#### Help

Calling `akamai help` will show basic usage info, and available commands. To learn more about a specific sub-command, use `akamai help <command> [sub-command]`.

#### List

Calling `akamai list` will show you a list of available sub-commands. If a command is not shown, ensure that the binary is executable, and in your `PATH`.

#### Get

The `get` command allows you to easily install new sub-commands from a git repository.

Calling `akamai get <repo>` will download and install the command repository to the `$HOME/.akamai-cli` directory.

For Github repositories, you can pass in `user/repo` or `organization/repo`.

#### Update

To update a sub-command installed with `akamai get`, you call `akamai update <command>`.

Calling `akamai update` with no arguments will update _all_ commands installed using `akamai get`

#### Sub-commands

To call a sub-command, use `akamai <sub-command> [args]`, e.g.

```sh
akamai property create example.org
```

### Custom commands

Akamai CLI also provides a framework for writing custom CLI commands. There are a few requirements:

1. The binary must be named `akamai-<command>` or `akamai<Command>`
2. Help must be visible when you run: `akamai-command help` and ideally, should allow for `akamai-command help <sub-command>`
3. If the action fails to complete, it should return a non-zero status code (however, `akamai` will only return `0` on success or `1` on failure)

You can use _any_ language to build commands, so long as the result is executable — this includes PHP, Python, Ruby, Perl, Java, Golang, JavaScript, and C#.

### Command Package Metadata

You *must* include a `cli.json` file to inform Akamai CLI about the command package and it's included commands.

`cli.json` allows you specify the command language runtime version, as well as define all commands included in package.

##### Example

```json
{
  "requirements": {
    "go": "1.8.0"
  },
  "commands": [
    {
      "name": "purge",
      "version": "0.1.0",
      "description": "Purge content from the Edge",
      "bin": "https://github.com/akamai/cli-purge/releases/download/{{.Version}}/akamai-{{.Name}}-{{.OS}}{{.Arch}}{{.BinSuffix}}"
    }
  ]
}
```

##### Format

- `requirements` — specify runtime requirements. You may specify a minimum version number or use `*` for any version. Possible requirements are:
  - `go`
  - `php`
  - `ruby`
  - `node`
  - `python`
- `commands` — A list of commands included in the package
  - `name` — The command name
  - `version` — The command version
  - `description` - The command description
  - `usage` - A usage string (shown after the command name)
  - `arguments` — A list of arguments
  - `bin` — A url to fetch a binary package from if it cannot be installed from source

The `bin` URL may contain the following placeholders:

- `{{.Version}}` — The command version
- `{{.Name}}` — The command name
- `{{.OS}}` — The current operating system
  - Possible values are: `windows`, `mac`, or `linux`
- `{{.Arch}}` — The current OS architecture
  - Possible values are: `386`, `amd64`
- `{{.BinSuffix}}` — The binary suffix for the current OS
  - Possible values are: `.exe` for windows
