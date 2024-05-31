---
title: Setup
---

> ## Objectives
> These instructions serve to help set up:
> - an editor,
> - a shell,
> - python,
> - git,
> - GitHub, and
> - an empty repository,
>
> either in the cloud (fast) or on a local computer (slow the first time).
{:.objectives}


> ## If this is your first time, use `Cloud Setup`
> If you have not installed and used these tools before, then in the interests of time for the lesson, please use the **Cloud Setup**. After the session you can repeat the setup using the Local Setup instructions.
>
> Support will be available for the rest of the week if you have trouble with setting up these tools locally!
{:.caution}

## Cloud Setup

You can get all the prerequisites for this lesson by using a GitHub Codespace.

- You will need a GitHub account from [https://github.com/join](https://github.com/join).
- Create a new repository by going to [https://github.com/new](https://github.com/new).
  - Ensure that the owner is you.
  - Initialize the repository with a README file by clicking the tickmark next to `Add a README file`.
- After the repository is created, click on the `< > Code` button, click the `Codespaces` tab, and click `Create codespace on main`. This will create an editor with a shell, `python` and `git` pre-configured for you.

## Local Setup

If you want to run this example on your own computer, you will need to install the parts independently.

### Editor

We recommend using the text editor VSCode from [https://code.visualstudio.com/](https://code.visualstudio.com/) for this lesson. We won't be using any of its special features, so if you prefer a different editor, please use that.

### Shell

This lesson uses shell commands which you can run in a terminal emulator. Depending on the operating system you use, you have different options.
- Linux – you can use any terminal emulator. Common options are `GNOME Terminal` and `Konsole (KDE)`.
- macOS – you can use any terminal emulator. Common options are: `Terminal.app`, `iTerm2`
- Windows – we recommend using the Windows Subsystem for Linux to install Linux (https://learn.microsoft.com/en-us/windows/wsl/install). You could also use Powershell, but some commands will be different and others unavailable.

### Python

You will need to have Python installed for this lesson.

To check if python is available in your shell, call `python3 --version`. You should see some output like:

```bash
% python3 --version
Python 3.12.3
```
{:code}

If this command returns something like `command not found: python3` then install python using the instructions on[https://docs.pytest.org/en/stable/getting-started.html](https://docs.pytest.org/en/stable/getting-started.html).

### Git & GitHub

You will need a GitHub account and an installation of Git.

If you don't already have a GitHub account you can sign up at [https://github.com/join](https://github.com/join).

For beginners, we recommend using GitHub desktop when working with GitHub – installation instructions at [https://desktop.github.com/](https://desktop.github.com/).

### Empty repository

Create a new repository using GitHub desktop.
- Open GitHub Desktop
- Select File > New Repository
- Specify the repository data:
  - Name: package-example (use dashes `-`, don't use underscores `_` for this name)
  - Local Path: pick somewhere which _isn't_ synced to a cloud provider like DropBox, OneDrive or iCloud – they can interfere with `git`.
  - Initialize the repository with a README.
  - Leave the .gitignore and License both as "None".
- Click `Create repository`
- Click `Publish repository` to make it available on GitHub.
- Click `Open in Visual Studio Code` _or_ open Visual Studio Code and click File > Open folder and select the directory with your new repository.

{% include links.md %}
