---
Title: Frequently Asked Questions
slug: faq
---

# What?


## What has SUSE announced?

SUSE Base Container Images (BCI) provides a repository of tested and certified
container images based on SUSE Linux Enterprise Server. The container images are
ready-to-go for enterprise use. SUSE maintains these images on a regular basis
so you can use them worry-free. The images are updated with the latest security
patches and features/functionalities are consistent with the base OS releases.

With Rancher 2.6, SUSE has announced full integration between Rancher and BCI
while ensuring the latest security standards.


## What is the Base Container Image (BCI)?

SUSE provides several Base Container Images to create applications based on the
application requirements.

SUSE provides truly open, flexible, secure container images and application
development tools for immediate use by developers, integrators, and operators.

BCI images are available via the [SUSE Container
Registry](https://registry.suse.com/) and are free to use and distribute, in
accordance with the [EULA](https://www.suse.com/licensing/eula/#bci).


## What does the BCI include?

BCI includes three sets of container images:

1. Pure SLE-based containers with a minimal set of packages: one with zypper,
   one without zypper but with rpm and one without both zypper and rpm, which
   adds flexibility to the development environment and removes unnecessary
   packages, making applications faster to deploy and to orchestrate.

2. Language Stack Container Images with a base environment for programming
   languages including Python, Node.js, Ruby, .NET, ASP.Net, Java (based on
   OpenJDK), Go and Rust.

3. Application Stack Container Images provide ready to use containerized
   applications like RMT or PostgreSQL.


## What are the benefits of Base Container Image (BCI)?

These are the main benefits:

- **Supportability**: While BCI images are free to use and redistribute, you can
  get support directly and leverage your applications to an enterprise level.

- **Availability**: Base Container Images are available on x86-64, arch64,
  s390x, and ppc64le.

- **Security**: Enables more secure container images, reducing the number of
  notifications from container vulnerability scanners.


## What are the use cases for the Base Container Images (BCI)?

BCI provides a stable, secure, and open ecosystem where you can develop and
deploy applications in a light and flexible environment while leveraging your
experience and the stability and security of the SLES (SUSE Linux Enterprise
Server) operating system.

From different perspectives, BCI offers several opportunities.

* Rancher users:
- Enable Rancher to build using stable, reliable, secure, and certified
  enterprise components.
- Leverage SUSE’s in-house OS knowledge while containerizing applications as the
  tools will be the same and there is no migration path needed. For instance,
  from zypper to other packaging systems, the BCI will behave as a container
  base as SLE would do for an OS.

* Developers:
- BCI can be deployed in any Linux host, helping migrations within a
  multi-vendor ecosystem, and avoiding vendor lock-in.
- Free BCI as an option in cases where a subscription is a hurdle in
  cloud-native environments

* ISVs (Independent Software Vendors):
- ISVs containerizing applications, using stable, reliable, secure, and
  certified enterprise OS.
- BCI offers ISVs a stable and performant ecosystem to build applications
  providing secure supply in the chain.
- ISVs need to run applications on a variety of hosts.


## What packages and libraries are available in BCI?

SUSE provides several BCIs, which allows developers to choose which one fits
their needs. At the same time, they provide developers with notable tools and
libraries like compilers, crypto libraries, and several OS tools, to recap some
of them.

- Package managers and tools like `zypper`, `rpm`, `sysctl` or `glibc`.
- Several Libraries: (`lib-acl`, `lib-crypto`, `lib-openssl`, `libldap`)


## What legal agreements are needed to build my products on BCI?

You must accept the [SLE BCI terms and
conditions](https://www.suse.com/licensing/eula/download/sbci/suse-base-container-image-licence-en.pdf).



# Why?

## Why did SUSE create Base Container Image (BCI)?

We want to provide truly open, flexible, and secure container images and
application development tools for immediate use by developers and integrators
without the lock-in imposed by alternative offerings.

To match the needs of regulated markets, SUSE plans to provide a specifically
hardened and certified SLE-based solution.


## On which Hardware Platforms will BCI be available? (x86_64, AARM64, POWER, IBM zSeries)?

BCIs are available on x86_64, aarch64, ppcle64 and s390x (.NET images are only
available on x86-64 now)


# How?


## Do I need a subscription to use BCI?

No, you can use them without a subscription.

BCI can be used for free, just pulling from the registry, they are ready to be
used with no support.

BCI under an active SLE subscription provides a full support and access to SUSE
repositories and a full set of tools and packages.


## Do I need a SUSE Linux environment to build images based on BCI?

No, you can build and run BCI in any environment that supports building based on
OCI compatible images.


## Do I need a SUSE Linux environment to deploy BCI?

No, you can run BCI in any certified Kubernetes deployment or any OCI compatible
runtime.


## Can I freely distribute applications built on BCI?

There is no restriction to redistribute application based on the free BCI, as
long as the repositories used are the free ones and no extra restriction applies
to the packages on top of the image SUSE provides.

The restrictions also apply if the application includes binaries from SLES
repositories. Adding third-party software implies restrictions at their
respective layers.

All redistribution policies are listed in the
[EULA](https://www.suse.com/licensing/eula/#bci).


## Can I distribute my BCI-based container images without using SUSE’s registry?

If BCI Images are free to use and distribute, you can use any registry to
distribute your application based on BCI.

Yes, you can distribute your bci-based applications as you want.


## Can I add non-BCI RPMs to a BCI image and still redistribute the resulting container image on a non-SUSE platform?

As part of your development process, you can add non BCI-RPMs to the images, as
everything added on top of the image offered is considered part of the
application or dependencies.

There is no restriction from SUSE to redistribute the result if you comply with
the EULA.


## Is BCI recommended for community projects?

Yes, absolutely.


## Will BCI receive updates?

Yes, we build BCI images out of the SUSE Linux Enterprise Server repository. We
build new BCI images for each new SLE (SUSE Linux Enterprise) Service Pack.

BCI images of a released Service Pack receive updates, e.g., regarding
continuous security updates.


## Will my application built on BCI be supported?

SUSE supports the available BCI images.

Applications shipped via the container image should be supported by its vendor
or developer.


## What is the BCI lifecycle?

The General Purpose BCI follows the General Support lifecycle of the SLE Service
Pack they are made for. The SUSE Linux Enterprise Server lifecycle can be found
on [suse.com/lifecycle](https://www.suse.com/lifecycle).

Application and Language Stack BCI has a lifecycle that is tied to the
respective application or language stack and not to the respective service
pack. For further details, consult the SUSE lifecycle page
[suse.com/lifecycle](https://www.suse.com/lifecycle).

BCI images are not supported in LTSS (Long Term Service Pack Support).


## Does BCI let me distribute my container images anywhere I want?

Yes, SUSE will never oversee what you do with your images and how you distribute
them. BCIs are freely distributable, and you can also distribute your
applications as you want if you comply with the EULA.


## Can I add non-BCI packages if something is missing from BCI?

Yes, but SUSE is supporting BCI as it comes from our registry. Adding packages
to BCI is considered part of the development process but is not directly
supported by SUSE.


## Where do I report bugs with BCI images?

Customers and partners with a subscription can use the regular channels to
report issues. Bugs can also be reported to
[bugzilla.suse.com](https://bugzilla.suse.com).


## Where do we get support for BCI images?

Support for BCI is available with an SLE subscription. Reach out to SUSE for the
details.

Questions and discussions can be posted to the [SUSE
Community](https://community.suse.com/topics/3776090).


## Can I request for packages to be added to the BCI images (`SLE_BCI` repo)?

Please start a thread on the SUSE Community for consideration.
