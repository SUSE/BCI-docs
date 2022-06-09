---
title: Building Container Images based on SLE BCI
slug: building-on-top-of-bci
---

{{< hint title=Summary >}}
Many tools for building container images exist in the ecosystem and our Base
Container Images can be used by all of them. This guide summarizes the most
important ones for our users:

- [Locally using docker, podman, or nerdctl](#using-docker-podman-and-nerdctl)
- [Docker or Podman in the Open Build
  Service](#building-from-a-dockerfile-using-docker-or-podman)
- [Kiwi in the Open Build
  Service](#building-a-derived-container-image-using-kiwi)
{{< /hint >}}

# Using docker, podman and nerdctl

The SLE Base Container Images are OCI compliant images and can thus be used
without any modifications in your `Dockerfile`. Visit
[`registry.suse.com`](https://registry.suse.com), find the container image that
fits your needs, and include it in the `FROM` line in your `Dockerfile` as follows:

```Dockerfile
FROM registry.suse.com/bci/node:16 as node-builder
WORKDIR /app/
COPY . /app/

RUN npm install && npm run build
```

Build it with your favorite container runtime:
{{< tabs "build_container_image">}}
{{< tab "Docker">}}
```Shell
docker build .
```
{{< /tab >}}
{{< tab "Podman" >}}
```Shell
buildah bud --layers .
```
{{< /tab >}}
{{< tab "nerdctl" >}}
```Shell
nerdctl build .
```
{{< /tab >}}
{{< /tabs >}}


# Using the Open Build Service

The [Open Build Service](https://openbuildservice.org/) can be used to build
container images, as explained in the
[documentation](https://openbuildservice.org/help/manuals/obs-user-guide/cha.obs.build_containers.html). It
supports building container images using Docker, Podman or
[kiwi](https://osinside.github.io/kiwi/).

## Building from a `Dockerfile` using docker or podman

The Open Build Service supports building container images using a `Dockerfile` file. However, you need to pay particular attention when creating offline builds with the Open Build Service. To build a
container image based on BCI follow the steps below.

1. Create a new package for a container image in an appropriate project where
   you have write access using [`osc`](https://github.com/openSUSE/osc/):
```ShellSession
❯ cd /path/to/the/project/
❯ osc mkpac my-container-image && cd my-container-image
```

2. Create a new repository for building container images for the
   project called `containerfile` by convention.

   Create this repository by inserting the following XML snippet into the
   project settings via `osc meta -e prj`:
```xml
  <repository name="containerfile">
    <path project="SUSE:Registry" repository="standard"/>
    <path project="SUSE:SLE-15-SP4:Update" repository="standard"/>
    <arch>x86_64</arch>
    <arch>aarch64</arch>
    <arch>s390x</arch>
    <arch>ppc64le</arch>
  </repository>
</project>
```
   Depending on the SLE version that you are targeting, you will have to adjust
   the version in line 3.

3. Add the following snippet into the project configuration. You can edit the
   project configuration via `osc meta -e prjconf`:
```
%if %_repository == "containerfile"
# if you prefer docker, use the following line or if you want to build using
# podman, replace docker with podman
Type: docker
%endif
```

4. Create a `Dockerfile` inside your the package `my-container-image` and set the
   base image of your container image using `FROM` as usual. The only difference
   is that you have to omit the `registry.suse.com` from the BCI URL and only
   use the build tag as illustrated below:

{{< tabs "dockerfiles_from">}}
{{< tab "bci-base">}}
```Dockerfile
FROM bci/bci-base:15.4
```
{{< /tab >}}
{{< tab "python" >}}
```Dockerfile
FROM bci/python:3.10
```
{{< /tab >}}
{{< tab "openjdk" >}}
```Dockerfile
FROM bci/openjdk:17
```
{{< /tab >}}
{{< /tabs >}}

5. Set the build tags using comments in the `Dockerfile`. A build tag is the
   equivalent of the `-t` flag passed to `docker build` on the command
   line. Since the Open Build Service invokes `docker build` itself, it has to
   take the build tags from some other place and the `Dockerfile` is used for
   that as shown below:
```Dockerfile
#!BuildTag: my-build-tag:latest
#!BuildTag: my-build-tag:0.1
#!BuildTag: my-other-build-tag:0.6
```

## Building a derived container image using kiwi

[kiwi](https://osinside.github.io/kiwi/) is a generic image building tool that
also supports building container images. It is tightly integrated into the Open
Build Service as the standard image building tool.

Building a container image based on SLE BCI is outlined in the following steps:


1. Create a new package for your container image in an appropriate project where
   you have write access using [`osc`](https://github.com/openSUSE/osc/):
```ShellSession
❯ cd /path/to/the/project/
❯ osc mkpac my-kiwi-container && cd my-kiwi-container
```

2. Create a new repository for building container images for your
   project. Repositories building using kiwi are called `images` by convention
   and that name will be used below as well. If you pick a different repository
   name, be sure to adjust it in all other places as well.

   Create this repository by inserting the following xml snippet into the
   project settings via `osc meta -e prj`:
```xml
  <repository name="images">
    <path project="SUSE:Registry" repository="standard"/>
    <path project="SUSE:SLE-15-SP4:Update" repository="standard"/>
    <arch>x86_64</arch>
    <arch>aarch64</arch>
    <arch>s390x</arch>
    <arch>ppc64le</arch>
  </repository>
```
   Depending on the SLE version that you are targeting, you will have to adjust
   the version in line 3.

3. Add the following snippet into the project configuration. You can edit the
   project configuration via `osc meta -e prjconf`:
```
%if "%_repository" == "images"
Type: kiwi
Repotype: none
Patterntype: none

Prefer: -libcurl4-mini
Prefer: -systemd-mini
Prefer: -libsystemd0-mini
Prefer: -libudev-mini1
Prefer: -udev-mini
Prefer: kiwi-boot-requires
Prefer: sles-release
Prefer: sles-release-MINI
Prefer: python3-kiwi

# needed for busybox image building (at minimum)
ExpandFlags: kiwi-nobasepackages

# needed for micro image
Prefer: -busybox-coreutils

Preinstall: !rpm rpm-ndb
Substitute: rpm rpm-ndb
Binarytype: rpm
%endif
```

4. Create a `kiwi.xml` inside your the package `my-kiwi-image`. Refer to
   a BCI using its build as outlined in the following examples:

{{< tabs "dockerfiles_from">}}
{{< tab "bci-base">}}
```xml
<image schemaversion="6.5" name="my-kiwi-image">
  <description type="system"><!-- omitted --></description>
  <preferences>
    <type image="docker" derived_from="obsrepositories:/bci/bci-base#15.4">
      <!-- remaining container settings here -->
    </type>
  </preferences>
  <!-- package & repository config here -->
</image>
```
{{< /tab >}}
{{< tab "python" >}}
```xml
<image schemaversion="6.5" name="my-kiwi-image">
  <description type="system"><!-- omitted --></description>
  <preferences>
    <type image="docker" derived_from="obsrepositories:/bci/python#3.10">
      <!-- remaining container settings here -->
    </type>
  </preferences>
  <!-- package & repository config here -->
</image>
```
{{< /tab >}}
{{< tab "openjdk" >}}
```xml
<image schemaversion="6.5" name="my-kiwi-image">
  <description type="system"><!-- omitted --></description>
  <preferences>
    <type image="docker" derived_from="obsrepositories:/bci/openjdk#17">
      <!-- remaining container settings here -->
    </type>
  </preferences>
  <!-- package & repository config here -->
</image>
```
{{< /tab >}}
{{< /tabs >}}

5. Set the build tags using comments in `kiwi.xml`:
```xml
<!-- OBS-AddTag: my-build-tag:latest my-build-tag:0.1 my-other-build-tag:0.6 -->
```

## Building Container Images based on your own images

You can build Container Images in the Open Build Service that are based on
other Images that you have been build in the Open Build Service. Proceed for
this as follows:

1. _Skip this step if your image is in the same project and repository as the
   image that you are building._

   Find the project and the repository corresponding to the container image that
   you would like to use as the base. You can leverage
   [registry.opensuse.org](https://registry.opensuse.org/cgi-bin/cooverview) for
   that by searching for container image and extracting the project and
   repository names (underlined in mint green and waterhole blue respectively):
   ![Ruby BCI on registry.opensuse.org](/images/registry.opensuse.org_bci_ruby.png)

   Add this project and repository to your project's repository configuration
   either by inserting a path entry via `osc meta -e prj`:
```xml
  <repository name="my_container_build_repository">
    <path project="$THE_PROJECT_NAME" repository="$THE_REPOSITORY_NAME"/>
    <!-- existing paths are here -->
    <!-- architectures -->
  </repository>
```
   Alternatively, you can add this repository via the web interface. For that
   navigate to the project's home page in the Open Build Service and click on
   the `Repositories` tab. There find the repository in which you build your
   container image and click on the green plus icon and enter the project name
   and the repository name in the appearing popup:

   ![repository view](/images/obs_repository_add.png)

2. Use the build tag of the container image in the `FROM` instruction in your
   `Dockerfile`. The build tag can be found in the `Dockerfile` of the container
   image via the comment `#!BuildTag: $TAG` or in a kiwi xml description via the
   comment `<!-- OBS-AddTag: $TAG -->`.

   A simpler way is to go to
   [registry.opensuse.org](https://registry.opensuse.org/cgi-bin/cooverview) and
   find the container image. The path on `registry.opensuse.org` is constructed
   from the images project, repository and build tag as outlined in the image
   below (the project is underlined in mint green, the repository in waterhole
   blue and the build tag in persimmon):
   ![Ruby BCI on registry.opensuse.org with the build tag
   underlined](/images/registry.opensuse.org_bci_ruby_build_tag.png)
