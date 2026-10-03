[![img](https://github.com/F4ban/codespaces.el/actions/workflows/check.yml/badge.svg)](https://github.com/F4ban/codespaces.el/actions/workflows/check.yml)
[![img](https://melpa.org/packages/codespaces-badge.svg)](https://melpa.org/#/codespaces)
[![img](https://img.shields.io/github/license/F4ban/codespaces.el.svg)](https://raw.githubusercontent.com/F4ban/codespaces.el/main/LICENSE)


## Codespaces.el

This package provides support for managing [GitHub Codespaces](https://github.com/features/codespaces) in Emacs and connecting to them via [TRAMP](https://www.gnu.org/software/tramp/). It provides a handy `completing-read` UI that lets you choose from all your created codespaces.

![img](./demo.gif)

Here is an example `use-package` declaration:

    (use-package codespaces
      :config (codespaces-setup)
      :bind ("C-c S" . #'codespaces-connect))

You will need to:

1.  Have the GitHub [command line tools](https://cli.github.com) (`gh`) installed.
    
    -   If you use `use-package-ensure-system-package`, Emacs can install this for you automatically:
    
        (use-package use-package-ensure-system-package :ensure t)
        (use-package codespaces
          :ensure-system-package gh
          :config (codespaces-setup))

2.  Authorize `gh` to access your codespaces:
    -   Running `gh codespace list` will verify if permissions are correctly set.
    -   You can grant the required permission by running `gh auth refresh -h github.com -s codespace`.

Because the TRAMP package, which underpins this package's functionality, connects to remote servers over SSH, your codespace needs to have an SSH server running on it. It normally is enabled by default. If the Docker image that your codespace uses doesn't have an SSH server, install `sshd` in your Dockerfile; for codespaces that use Debian-based images, you can add the following to `.devcontainer/devcontainer.json`:

    "features": {
      "ghcr.io/devcontainers/features/sshd:1": {
        "version": "latest"
      }
    }


# Recommended Configurations

I *strongly* recommend you customize `vc-handled-backends` and remove the ones that you don't use. I suffered considerable lag to Codespace instances before I did so.

    (setq vc-handled-backends '(Git))

Use the host's PATH as TRAMP's [remote path](https://www.gnu.org/software/emacs/manual/html_node/tramp/Remote-programs.html):

    (add-to-list 'tramp-remote-path 'tramp-own-remote-path)

Use SSH connection sharing to reduce the number of new SSH connections that TRAMP needs to spawn for refreshing buffers. In your `~/.ssh/config` file, add the lines:

    ControlMaster auto
    ControlPath ~/.ssh/sockets/%C
    ControlPersist 10m

And to disable TRAMP's default ControlMaster settings in your Emacs config, add:

    (setq tramp-ssh-controlmaster-options nil)


# User-facing commands

-   `codespaces-connect` brings up a list of codespaces, and upon selection opens a Dired buffer in `/workspaces` (the default Codespaces location).
-   `codespaces-start` brings up a list of inactive codespaces and upon selection spawns a task that starts the selected codespace.
-   `codespaces-stop` does the same but for stopping active codespaces.
-   `codespaces-create` prompts for a repository, branch, and machine to then create a Codespace. Default branch is fetched via gh.
-   `codespaces-delete` brings up a list of codespaces, and upon selection deletes the selected codespaces.


# Missing features

-   Completion should sort codespaces by most-recently-used.
-   Should have a nice `transient.el` UI.


# Credits

-   Thanks to [Bas Alberts](https://github.com/anticomputer) for writing the code to register `ghcs` as a valid TRAMP connection method.
-   Thanks to [Patrick Thomson](https://github.com/patrickt) for writing the entire logic behind codespaces.el.
-   Thanks to [Fabian Markl](https://markl.dev) for maintaining this package.


# License

GPL3

