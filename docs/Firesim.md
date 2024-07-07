# Firesim Getting Started Guide

reference：https://docs.fires.im/en/stable/FireSim-Basics.html

## What is Firesim?

**Introduction：**

FireSim is an open-source **FPGA-accelerated full-system hardware simulation platform** that makes it easy to validate, profile, and debug RTL hardware implementations at 10s to 100s of MHz. FireSim simplifies co-simulating ASIC RTL with cycle-accurate hardware and software models for other system components (e.g. I/Os). FireSim can productively scale from individual SoC simulations hosted on on-prem FPGAs (e.g., a single Xilinx Alveo board attached to a desktop) to massive datacenter-scale simulations harnessing hundreds of cloud FPGAs (e.g., on Amazon EC2 F1).

**Usage model：**

We choose  **"Single-Node Simulations Using One or More On-Premises FPGAs"** usage model. In this usage model, FireSim allows for simulation of targets consisting of individual SoC designs (e.g., those produced by [Chipyard](https://chipyard.readthedocs.io/)) at **150+ MHz running on on-premises FPGAs**, such as those attached to your local desktop, laptop, or cluster. Just like on the cloud, the FireSim manager can automatically distribute and manage jobs on one or more on-premises FPGAs, including running complex workloads like SPECInt2017 with full reference inputs.

There exists other usage models，such as Single-Node Simulations Using Cloud FPGAs and Datacenter/Cluster Simulations on On-Premises or Cloud FPGAs. For more details, you can check out the official documentation.

## Background

**FireSim Infrastructure Diagram :**

<img src="https://docs.fires.im/en/stable/_images/firesim_env.png" alt="FireSim Infrastructure Setup" style="zoom: 30%;" />

Machines used to build and run FireSim simulations are broadly classified into three groups:

**Manager Machine**

This is the main host machine (e.g., your local desktop or server) that you will “do work” on. This is where you’ll clone your copy of FireSim and **use the FireSim Manager to deploy builds/simulations** from.

**Build Farm Machines**

These are a collection of local machines (“build farm machines”) that are used by the FireSim manager to **run FPGA bitstream builds**. 

**Run Farm Machines**

These are a collection of local machines (“run farm machines”) **with FPGAs attached** that the manager manages and **deploys simulations onto**.

## System Setup

We run Firesim on Xilinx Alveo U280 server.

## FireSim Repo Setup

**1.Fetch FireSim’s sources.**

```shell
 git clone https://github.com/firesim/firesim
 cd firesim
 # checkout latest official firesim release
 # note: this may not be the latest release if the documentation is not up to date
 git checkout 1.18.0
```

The Firesim repository contains many submodules. It's advisable to execute the following command beforehand to install all submodules:

```
git submodule update --init --recursive
```

This command ensures that all necessary submodules are cloned and initialized, preparing the repository for subsequent installation scripts or procedures.

If the command `git submodule update --init --recursive` fails to execute successfully, you can manually clone each submodule that wasn't cloned properly. Here’s how you can proceed:

1. **Check Submodules**: First, list all the submodules in your repository using the command:

   ```
   git submodule
   ```

2. **Manually Clone Submodules**: For each submodule listed, if it hasn't been cloned properly or is missing:

   - Navigate into the submodule directory. For example:

     ```
     cd path/to/submodule/../
     ```

   - Manually clone the submodule from its source repository. For instance:

     ```
     git clone https://github.com/username/submodule.git
     ```

3. **Delete existing submodules:**

   ```
   git rm <path-to-submodule>
   ```

4. **Add manually clone submodules:**

   ```
   git submodule add https://github.com/username/submodule.git path/to/submodule/
   ```

   

**2.Set up a default software environment using Conda.**  

Replace `REPLACE_ME_USER_CONDA_LOCATION` in the command below with your chosen path to install conda and run it:

```bash
./scripts/machine-launch-script.sh --prefix REPLACE_ME_USER_CONDA_LOCATION
```

If you are not sudoer, comment the following code in `./scripts/machine-launch-script.sh` :

```bash
    SUDO=""
    # prefix_parent=$(dirname "$CONDA_INSTALL_PREFIX")
    # if [[ ! -e "$prefix_parent" ]]; then
    #     mkdir -p "$prefix_parent" || SUDO=sudo
    # elif [[ ! -w "$prefix_parent" ]]; then
    #     SUDO=sudo
    # fi
```

If you have network problems, please try again！



**3.Build-setup**

When you are in conda firesim environment, run 

```bash
./build-setup.sh
```

While this command may seem simple at first glance, you may encounter various issues during its execution. In such cases, your task is to identify the commands in the script that are causing errors and resolve them one by one. Here are some common errors and their solutions.

ChecksumMismatchError: Conda detected a mismatch between the expected content and downloaded content, try

```bash
conda clean -all
or 
cd ~/conda/pkgs/
rm -rf *
```

When executing `conda-lock install --conda $(which conda) -p $RDIR/.conda-env $LOCKFILE` , using the Tsinghua conda mirror significantly improves download speeds and helps avoid most network issues. 

Modify the contents of the `/firesim/conda-reqs/conda-reqs.conda-lock.yml` file by replacing all occurrences of `https://conda.anaconda.org/` with `https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/`. This change will enable the use of the Tsinghua mirror. For example, replace:

```
https://conda.anaconda.org/conda-forge/linux-64/_libgcc_mutex-0.1-conda_forge.tar.bz2
```

with:

```
https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge/linux-64/_libgcc_mutex-0.1-conda_forge.tar.bz2
```

When executing `./build-setup.sh --force $SKIP_TOOLCHAIN_ARG -s 1 -s 4 -s 5 -s 6 -s 7 -s 8 -s 9` , you should dive into `/firesim/target-design/chipyard/scripts`  for more details.

