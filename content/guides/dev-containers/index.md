---
Title: How To Use BCIs As VScode Development Containers
slug: vscode-dev-containers
resources:
  - name: vscode
    src: "vscode.png"
    title: VS Code with source mounted in Development Container. Open terminal is a console within the container.
  - name: popup
    src: "popup.png"
    title: Pop-up asking if you want to open the code within a Development Container
---

{{< img name="vscode" size="large" lazy=false >}}

Visual Studio Code has a feature called [Development Containers](https://code.visualstudio.com/docs/remote/create-dev-container).
This is part of the built-in functionality to work with remote containers. The
SLE Language Stack BCIs make a great choice to use as a development
environment.

## Why Use The SLE BCI In Development Containers

There are two reasons the BCI language stack containers are useful for development.

First, you develop in the same environment as when you use a BCI language stack
image to build or run your application. Being able to develop and build or run
your application in the same environment setup enables you to discover quirks
and issues that are related to the way your application works in the environment.

Second, when you have a team of people working on an application,
all developers will have the same environment to work in. Whether they
are on Windows, macOS, or Linux their will be the same.

## Docker Socket Required

VS Code creates the development containers using the Docker Engine and
it communicates with it over the Docker socket. That means you need a Docker
socket available on your system.

[Rancher Desktop](https://rancherdesktop.io) is our recommended app for working
with containers. It is available for Windows, macOS, and Linux. Alternatively,
you can use another tool such as Docker Desktop or the Docker Engine itself
if you are using Linux.

{{< hint type=note >}}
VS Code mounts your code from the local system inside the container. This works
best when the container runtime is on your local system as opposed to remote.
While possible to do this with the container runtime on a remote system, this
guide does not cover running the development container on a remote machine.
{{< /hint >}}

## Development Container Basics

Dev containers enable you to mount any folder inside a container where you can
specify the environment. Debugging, extensions, and other features work with
the code as it is mounted within the container rather than the location on the
local file system.

## Development Container Configuration

The configuration for Development Containers is stored in a file named
`.devcontainer/devcontainer.json`. When you open up a codebase with this
configuration file present, VS Code will read the configuration and present you
with an option to use a Development Container.

There are two ways to specify where to get the container from. You can point it
at an image or you can point it at a _Dockerfile_ to build the image from. Since
VS Code needs some additional packages installed you can't simply point it at
a BCI image.

To illustrate using the `Dockerfile` method we can look at a setup for the Go
programming language. A `Dockerfile` placed in the `.devcontainer` directory
would look like:

```dockerfile
FROM registry.suse.com/bci/golang:1.18

# Install tools needed by Visual Studio Code Remote Development Containers
RUN zypper --non-interactive install -y tar git gzip
```

VS Code needs git, gzip, and tar installed which are not present in the Go BCI
image by default.

While this example is targeted at Go, it will work for other languages where
there is a [BCI language stack]({{< relref "/documentation/language-stack-bci.md" >}})
available.

This file needs to be referenced in the `devcontainer/devcontainer.json` file.
For example, a `devcontainer.json` could look like the following:

```json
{
	"name": "Golang",
	"build": {
		"dockerfile": "Dockerfile"
	}
}
```

If you open up a project with these files in them VS Code will prompt you to open
them in a container as the image below illustrates.

{{< img name="popup" size="small" lazy=false >}}

This example JSON configuration file is in its simplest form. You can learn more
details about the additional configuration in the
[VS Code documentation for Development Containers](https://code.visualstudio.com/docs/remote/create-dev-container).
