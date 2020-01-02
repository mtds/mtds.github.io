---
layout: post
title:  "Packaging and distribute software on HPC clusters"
date:   2020-01-02 19:52:25 +0200
categories: linux hpc sysadmin
---

With the term _packaging_ here I am mostly referring to the act of making
a software (compiler, scientific libraries, etc.) available to the users
on a shared computing environment (in this post, an HPC cluster).

_Distributing_ software on an HPC cluster, even in case of small instances with
less than 20 nodes, it's not an easy task. Multiple solutions are available to
the admins, some of them are battle tested some are new and some can be considered
on the 'bleeding edge' side.

The following is a list that (to the best of my knowledge) summarize as much as
possible the available choices for both tasks.

**NOTE**: for the sake of simplicity I am only considering GNU/Linux based installations,
on _x86_ hardware environments altough some of this methods can be applied on _ARM_ based
clusters. Very specific hardware/software combinations (e.g. _Cray_) are also not considered 
here.

## Distribution methods

* **Packages**: highly dependent on the Linux distribution of choice, it's a
relatively quick (although not always easy) way to install software on the
cluster. Most of the problems related to this approach rely on dependency
complexity, in particular toward the rest of the OS. After a certain point
it will be next to impossible to avoid breaking the installation of the base
OS (e.g. Python packages and their dependencies are an easy example).

Possible workaround based on packages (altough focused only on _RHEL/CentOS_) 
is based on [OpenHPC](https://openhpc.community/).

* **NFS export(s)**: centralized setup, entirely under control of the admins. 
Easy to setup but not flexible enough: it's next to impossible for the users
to provide software to other users/groups without administrative privileges. 
On the other hand, admins will spend a lot of time installing multiple versions 
of compilers/libraries or frameworks,etc.

* **High performance File Systems**: FS like _Lustre_, _GPFS_ and the like provides
an additional method to distribute software in a centralized way. It's not a flexible
solution in any case, since the users may ended up polluting their directories with
multiple neglected installations of different softwares and in some case this may
also cause performances issues on some filesystems given the increase of metadata
operations involved in dealing with a huge amount of very small files.

* **CVMFS**: The [CernVM File System](https://cernvm.cern.ch/portal/filesystem) ("CernVM-FS" 
or "CVMFS") is a tool that allows for efficient global distribution of software and data 
that does not change frequently. Its bigger user base is within the high energy physics 
(HEP) community. CernVM-FS is implemented as a POSIX read-only file system in user space 
(a FUSE module). Files and directories are hosted on standard web servers and mounted in 
the universal namespace _/cvmfs_.

## Build Tools

Third party tools to build HPC software outside of the of the ones provided by a Linux distribution:

* [Conda](https://conda.io)
* [EasyBuild](https://easybuilders.github.io/easybuild/)
* [Spack](https://github.com/spack/spack)

## External Package managers 

For general software installations, the following tools supports versioning, rollbacks and binary reproducibility:

* [Guix](https://guix.gnu.org/)
* [Nix](https://nixos.org/nix/)

In particular, [_GUIX-HPC_](https://hpc.guix.info/) should also be mentioned as an ongoing
effort of using _GUIX_ to package software specifically for HPC environments.

## Containers

Traditional Linux containers are really just ordinary processes on a Linux system. These groups
of processes are isolated from other groups of processes using resource constraints (control groups
[_cgroups_]), Linux security constraints (Unix permissions, capabilities, SELinux, AppArmor, seccomp,
etc.), and kernel namespaces (PID, network, mount, etc.).

While containers are meant to be a general solution to package different kind of software and were
not developed with HPC as the main case study, the following two tools proves to be a good fit in
packing and distribute software in a cluster:

* [Singularity](https://sylabs.io)
* [CharlieCloud](https://hpc.github.io/charliecloud/)

An interesting example on how to use containers to distribute software: 
* [Building and using containers at HPC centres for the ATLAS experiment](https://www.epj-conferences.org/articles/epjconf/pdf/2019/19/epjconf_chep2018_07005.pdf)

## Resources

* [Comparison between Conda,Guix,Spack,Easybuild (FOSDEM 2018)](https://archive.fosdem.org/2018/schedule/event/installing_software_for_scientists/)
* [Combining CVMFS, Nix, Lmod, and EasyBuild at Compute Canada (FOSDEM 2018)](https://archive.fosdem.org/2018/schedule/event/computecanada/)
* [10 years of EasyBuild (HPCKP 2019 - PDF slides)](https://hpckp.org/wp-content/uploads/2019/08/7-K.Hoste-EasyBuild.pdf)

