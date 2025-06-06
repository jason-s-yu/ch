# `ch` container helper

<div>
  <a href="github.com/camerondurham/ch">
    <img align="left" src="https://img.shields.io/github/v/release/camerondurham/ch?include_prereleases" />
  </a>

  <a href="github.com/camerondurham/ch">
    <img align="left" src="https://img.shields.io/github/go-mod/go-version/camerondurham/ch" />
  </a>

  <a href="https://github.com/camerondurham/ch">
    <img align="left" src="https://img.shields.io/github/downloads/camerondurham/ch/total" />
  </a>

  <a>
    <img align="left" src="https://img.shields.io/badge/maintenance-passively--maintained-yellowgreen.svg"/>
  </a>

  <a href="github.com/camerondurham/ch">
    <img align="left" src="https://github.com/camerondurham/ch/actions/workflows/test.yml/badge.svg" href="https://github.com/camerondurham/ch/actions/workflows/test.yml" />
  </a>
</div>

<br>

`ch` is a command-line interface for using Docker containers as development environment. It enables users to setup a complex
container configuration once (saved in `~/.ch.yaml` on macOS/Linux) and the easily access the environment with a few commands.

This was designed to generalize how we develop C++ code in CSCI 104 to be portable enough to use
whichever Docker container you choose. Of course, this project would not be possible without the reference
of [docker/cli](https://github.com/docker/cli) for examples of how to use the Docker Engine API.

**2023-05-13 Update**

This project This was built before I knew anything about [Nix](https://nixos.org) or [nix-shell](https://nixos.wiki/wiki/Development_environment_with_nix-shell). I've realized those tools are far superior for addressing this problem where a specific compiler and debugging tools are required.

## Installation

You can follow the installation instructions below to install
`ch` or see
the [csci104/docker](https://github.com/csci104/docker) repository
instructions and automated setup scripts for a C++ based
development environment.

### Prerequisites

Please make sure that your machine meets the requirements for Docker:

- <a href="https://docs.docker.com/docker-for-windows/install/" target="_blank">Windows host requirements</a>

- <a href="https://docs.docker.com/docker-for-mac/install/" target="_blank">Mac host requirements </a>

### Step 0: Install WSL2 (Windows only)

If you are using macOS or Linux operating system, you can skip this section.
If you are running Windows, you must install the Windows Subsystem for Linux 2 (WSL2) before installing Docker.

Follow the instructions below to install WSL2 on your machine: <a href="https://docs.microsoft.com/windows/wsl/install-win10" target="_blank">Windows Subsystem for Linux Installation Guide</a>

### Step 1: Install Docker

Install Docker Desktop from <a href="https://www.docker.com/products/docker-desktop" target="_blank">the site</a>.

### Step 2: Install `ch`

Run the following commands below to download and run the install script for your operating system.

The install script just downloads the latest release from this repository, unpacks, and adds the `ch` binary to your path.

#### macOS/Linux

If you run macOS or a Linux OS, follow this section to install the `ch` binary.

**Option 1: (recommended for macOS/Linux) use homebrew**

```bash
brew tap camerondurham/tap
brew install camerondurham/tap/ch
```

**Option 2: run a bash script**

Run in your preferred Terminal to download and run the install script for Unix:

```bash
 bash <(curl -s https://raw.githubusercontent.com/camerondurham/ch/main/scripts/install-ch.sh)
```

You can check out the source code [here](https://github.com/camerondurham/ch/blob/main/scripts/install-ch.sh).

Depending on your default shell (usually `bash` or `zsh`), you will have to source your `~/.bashrc` or `~/.zshrc` to add
`ch` to your `PATH`.

---

#### Windows

If you use Windows, follow this section to install the `ch` binary.

Run PowerShell as Admin and execute this command to download and run the install script for Windows:

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://raw.githubusercontent.com/camerondurham/ch/main/scripts/install-ch.ps1'))
```

You can check out the source code [here](https://github.com/camerondurham/ch/blob/main/scripts/install-ch.ps1).

You may need to restart your machine or log out so `ch` is added to your `Path`.

### Step 3: Setup Your First Environment

See [commands documentation](#commands) or the [example commands](#more-examples) for how to create your first
environment.

#### Create the CSCI104 Environment

Where `csci104-work` is your homework folder in the current directory. Alternatively, you can provide the absolute path
to wherever your homework is on your machine.

This environment is based on this repository: [csci104/docker](https://github.com/csci104/docker)

1. use `ch create` to create and save the environment settings
   ```bash
   ch create csci104 \
       --image usccsci104/docker:20.04 \
       --volume csci104-work:/work  \
       --security-opt seccomp:unconfined \
       --cap-add SYS_PTRACE \
       --shell /bin/bash
   ```
1. start the environment with a terminal session
   ```bash
   ch shell csci104 --force-start
   ```

#### Create the CSCI 350 Environment

The commands here assume `csci350-work` is your homework folder in the current directory. Alternatively, you can provide the absolute path
to wherever your homework is on your machine. For Windows, your volume command should look like `--volume "C:\Users\user\path\to\csci350:/xv6_docker"`, on macOS your command should look like `--volume /Users/username/path/to/csci350:/xv6_docker`.

This environment is based on the this repository: [camerondurham/cs350-docker](https://github.com/camerondurham/cs350-docker)

1. find the absolute path to your `csci350` directory where you keep your homework (see [Filepaths in terminal](https://github.com/csci104/docker#filepaths-in-the-terminal) wiki from csci104/docker if you are having issues)

   1. (macOS/Linux) navigate to your directory in the terminal and run `pwd`, the output should be something like `/Users/username/path/to/csci350`
   2. (Windows Powershell) navigate to the directory in Powershell and run `Get-Location`, you will want the output like `C:\Users\Username\path\to\csci350`

1. use `ch create` to create and save the environment settings, replacing `PATH_TO_YOUR_WORKDIR` with the path from step 1.
   ```bash
   ch create csci350 \
       --image camerondurham/cs350-docker:v1 \
       --volume PATH_TO_YOUR_WORKDIR:/xv6_docker \
       --security-opt seccomp:unconfined \
       --port 7776:22 \
       --port 7777:7777 \
       --port 25000:25000 \
       --cap-add SYS_PTRACE \
       --shell /bin/bash \
       --privileged \
       --platform linux/amd64
   ```
1. start the environment with a terminal session
   ```bash
   ch shell csci350 --force-start
   ```

## What is this?

What's the use case for this tool? Good question! This tool is designed to make it easier to use a specific, isolated development environment. For classes
such as [CSCI 104](https://bytes.usc.edu/cs104/) (Data Structures and Algorithms) and CSCI 350 (Operating Systems) at USC, the legacy way of writing code in the class was using a large VM image inside Virtual Box,
or if you're lucky, VMWare. A more efficient and arguably smoother workflow involves setting using a Docker container with the class's compilers and
development tools installed. `ch` offers a consistent interface to configure and access these environments. See below for the commands to create
environments for these classes.

## Commands

### `ch create`

create docker environment, specify Dockerfile to build or image to pull

```txt
Usage:
  ch create ENVIRONMENT_NAME {--file DOCKERFILE|--image DOCKER_IMAGE} [--volume PATH_TO_DIRECTORY] [--shell SHELL_CMD] [[--cap-add cap1] ...] [[--security-opt secopt1] ...] [flags]

Flags:
      --cap-add stringArray        special capacity to add to Docker Container (syscalls)
      --context string             context to build Dockerfile (default ".")
      --cpus float                 number of CPUs to allocate to the container (default: 0.0 means all available)
  -f, --file string                path to Dockerfile, should be relative to context flag
  -h, --help                       help for create
  -i, --image string               image name to pull from DockerHub
  -p, --port stringArray           bind host port(s) to container
      --privileged                 run container as privileged (full root/admin access)
      --replace                    replace environment if it already exists
      --security-opt stringArray   security options
      --shell string               default shell to use when logging into environment (default "/bin/sh")
      --version                    version for create
  -v, --volume stringArray         volume to mount to the working directory
      --platform                   platform for image (e.g. linux/amd64, default is local platform)

```

### `ch list`

list all saved configs

```txt
Usage:
  ch list [ENVIRONMENT_NAME]
```

### `ch delete`

delete an environment from your `.ch.yaml` config file

```txt
Usage:
  ch delete ENVIRONMENT_NAME [flags]
```

### `ch start`

start docker container in background and save container ID to config file

```txt
Usage:
  ch start ENVIRONMENT_NAME
```

### `ch shell`

run docker shell in docker environment

```txt
Usage:
  ch shell ENVIRONMENT_NAME [flags]

Flags:
  -f, --force-start   autostart the environment if not running
```

### `ch stop`

stop running container/environment

```txt
Usage:
  ch stop ENVIRONMENT_NAME
```

### `ch running`

list all running environments

```txt
Usage:
  ch running
```

### `ch update`

update your Docker image to the latest version or rebuild the container

```txt
Usage:
  ch update [ENVIRONMENT_NAME] [flags]
```

### `ch upgrade`

check if you are running the latest version of `ch` and print install commands if an upgrade is available

```txt
Usage:
  ch upgrade
```

## More Examples

```shell script
# create environment
ch create ENVIRONMENT_NAME {--file DOCKERFILE|--image DOCKER_IMAGE} [--volume PATH_TO_DIRECTORY] [--shell SHELL_CMD] [--port HOST:CONTAINER] [--security-opt SECURITY_OPT]

# create an environment with a non-dockerhub image
ch create al2 --image public.ecr.aws/amazonlinux/amazonlinux:2 --shell /bin/bash --volume ./project/files

# create a csci104 docker image
ch create csci104 --image usccsci104/docker --shell /bin/bash --volume ./project/files/

# start container - essentially docker run -d IMAGE
ch start cs104

# get shell into environment - essentially docker exec -it CONTAINER_NAME
ch shell cs104

# stop container
ch stop cs104

# update docker image for environment
ch update csci104

# list all environments
ch list

# list all running environments
ch running
```
