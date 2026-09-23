---
layout: post
title:  "Packaging and distributing software on HPC clusters"
date:   2020-01-02 19:52:25 +0200
categories: linux hpc sysadmin
---

With the term _packaging_ here I am mostly referring to the act of making
a software (compiler, scientific libraries, etc.) available to the users
on a shared computing environment (in this post, an HPC cluster).

_Distributing_ software on an HPC cluster, even in case of small instances with
less than 20 nodes, is not an easy task. Multiple solutions are available to
the admins, some of them are battle tested, some are new and some can be
considered on the 'bleeding edge' side.

The following is a list that (to the best of my knowledge) summarizes as much
as possible the available choices for both tasks.

**NOTE**: for the sake of simplicity I am only considering GNU/Linux based
installations. The examples focus on _x86_ hardware, but _ARM/aarch64_ is now
mainstream on HPC (e.g. NVIDIA Grace Hopper, AWS Graviton) and most of these
methods apply to both. Very specific hardware/software combinations (e.g.
_Cray_) are also not considered here.

## Distribution methods

* **Packages**: highly dependent on the Linux distribution of choice, it's a
  relatively quick (although not always easy) way to install software on the
  cluster. Most of the problems related to this approach rely on dependency
  complexity, in particular toward the rest of the OS. After a certain point
  it will be next to impossible to avoid breaking the installation of the base
  OS (e.g. Python packages and their dependencies are an easy example).

  A useful workaround, now supporting multiple Linux distributions and
  processor architectures (not only RHEL/CentOS), is
  [OpenHPC](https://openhpc.community/). Note that the original _CentOS_ is
  end of life; the community RHEL clones are now **Rocky Linux** and
  **AlmaLinux**.

* **NFS export(s)**: centralized setup, entirely under control of the admins.
  Easy to setup but not flexible enough: it's next to impossible for the users
  to provide software to other users/groups without administrative privileges.
  On the other hand, admins will spend a lot of time installing multiple
  versions of compilers/libraries or frameworks, etc.

* **High performance File Systems**: FS like _Lustre_, _GPFS_, _BeeGFS_,
  _DAOS_ and the like provide an additional method to distribute software in
  a centralized way. It's not a flexible solution in any case, since the users
  may end up polluting their directories with multiple neglected installations
  of different softwares and in some case this may also cause performance
  issues on some filesystems given the increase of metadata operations
  involved in dealing with a huge amount of very small files.

* **CVMFS**: the [CernVM File System](https://cernvm.cern.ch/portal/filesystem)
  ("CernVM-FS" or "CVMFS") is a tool that allows for efficient global
  distribution of software and data that does not change frequently. Its
  biggest user base is within the high energy physics (HEP) community, but it
  is now also widely used to **serve prebuilt binary caches** (e.g. EasyBuild
  or Spack artifacts) as a read-only POSIX mount at _/cvmfs_.

* **Binary caches and mirrors**: instead of building software on the cluster,
  admins can distribute **prebuilt binaries** that users download and install.
  Examples include [Spack build caches](https://spack.readthedocs.io/en/latest/binary_caches.html),
  Nix binary caches, and CVMFS-backed EasyBuild/Spack software. This sharply
  reduces build time and is increasingly the default for large shared stacks.

## Module systems

All the methods above describe how software gets **to** the cluster; the module
system describes how users **access** it. [Environment
Modules](http://modules.sourceforge.net/) and, more commonly on HPC,
[Lmod](https://github.com/TCH-RC/Lmod) let users toggle software versions with
`module load`/`module swap` without altering the base OS. Most of the build
tools below emit module files, so the canonical HPC stack is the triad
**EasyBuild + Lmod + CVMFS**: EasyBuild builds the software, Lmod exposes it,
and CVMFS distributes the read-only payload.

## Build Tools

Third party tools to build and manage HPC software outside of the ones provided
by a Linux distribution. These are **build and management frameworks** whose
output is typically a set of module files (see [Module systems](#module-systems)):

* [Conda](https://conda.io) creates isolated, versioned environments;
  **conda-forge** is the main community channel and **mamba** is the standard
  fast solver.
* [EasyBuild](https://www.easybuild.io/) (v5.x) remains the de facto HPC build
  framework. It pairs with Lmod and is the engine behind
  [EESSI](https://eessi.io/), a standardized environment for toolchains and
  scientific software.
* [Spack](https://spack.io/) emphasizes a self-contained, relocatable install
  tree and first-class **binary distribution** via build caches and mirrors,
  including OCI/Docker registries and CVMFS.

## Package managers

For general software installations, the following tools support versioning,
rollbacks and binary reproducibility:

* [Guix](https://guix.gnu.org/) is a transactional, per-user package manager
  with **bit-for-bit reproducible** builds. The [Guix-HPC](https://hpc.guix.info/)
  effort (now including the Guix-Science channel) packages HPC software and
  supports cluster-wide installs as well as relocatable tarballs via
  `guix pack -RR`.
* [Nix](https://nixos.org/nix/) has become much more mainstream on HPC. It can
  run as a full OS (NixOS), as a per-user install on shared nodes, and it
  leverages **binary caches**; serving the Nix store read-only over CVMFS is a
  common pattern (see the Compute Canada reference below).

## Containers

Traditional Linux containers are really just ordinary processes on a Linux
system. These groups of processes are isolated from other groups of processes
using resource constraints (control groups [_cgroups_]), Linux security
constraints (Unix permissions, capabilities, SELinux, AppArmor, seccomp, etc.),
and kernel namespaces (PID, network, mount, etc.).

While containers were not developed with HPC as the main case study, they are a
strong fit for packaging and distributing software on a cluster. On HPC the
runtime must be **rootless and unprivileged** (no daemon, no privilege
escalation) and must share GPU and MPI access with the host. The two tools that
best meet these constraints are [Apptainer](https://apptainer.org/) (formerly
Singularity) and [CharlieCloud](https://charliecloud.io/).

[Apptainer](https://apptainer.org/) is the de facto HPC container runtime.
Renamed from Singularity in 2022 and now a Linux Foundation project, it runs
unprivileged, uses the single-file **SIF** format, is compatible with
Docker/**OCI** images, and has first-class GPU and MPI support. It pulls
directly from OCI registries (Docker Hub, GHCR, Harbor), which have become the
universal distribution format. Docker itself is generally not HPC-friendly
because it requires root or elevated privileges. CharlieCloud takes a lighter
approach with no daemon.

Today the dominant container use case is **AI/ML workloads** (CUDA, ROCm,
PyTorch, LLM serving), where containers plus CVMFS are the standard way to ship
heavy, fast-moving stacks.

An interesting example of using containers to distribute software is the ATLAS
experiment's approach[^a4t9x].

## Resources

* [Combining CVMFS, Nix, Lmod, and EasyBuild at Compute Canada](https://archive.fosdem.org/2018/schedule/event/computecanada/) (FOSDEM 2018)
* [Comparison between Conda, Guix, Spack, and EasyBuild](https://archive.fosdem.org/2018/schedule/event/installing_software_for_scientists/) (FOSDEM 2018)
* Ten years of EasyBuild[^k7b2m] (HPCKP 2019)
* [EESSI](https://eessi.io/), the European Environment for Scientific Software Installations (HPCWire Reader's Choice 2024, paper[^e5s3q])
* [Apptainer documentation](https://apptainer.org/docs/user/) (formerly Singularity)
* [Lmod](https://github.com/TCH-RC/Lmod) and [Environment Modules](http://modules.sourceforge.net/)
* [Nix](https://nixos.org/nix/) on HPC

[^a4t9x]: [Building and using containers at HPC centres for the ATLAS experiment (CHEP 2018, PDF)](https://www.epj-conferences.org/articles/epjconf/pdf/2019/19/epjconf_chep2018_07005.pdf)
[^k7b2m]: [10 years of EasyBuild, K. Hoste (HPCKP 2019, PDF slides)](https://hpckp.org/wp-content/uploads/2019/08/7-K.Hoste-EasyBuild.pdf)
[^e5s3q]: [EESSI open-access paper](https://doi.org/10.1002/spe.3075)
