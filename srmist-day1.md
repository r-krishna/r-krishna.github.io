---
layout: guide
title: SRMIST Day 1 | RFD4-Proteina on native Windows
permalink: /srmist-day1/
---

# RFD4-Proteina on native Windows

This walkthrough takes you from a fresh clone of the `experimental/windows-cu132` branch to a first GPU inference run using the short monomer example included in the repository. Run the commands in **PowerShell on x64 Windows**. The Windows support is experimental. The branch names the RTX 4500 as its target and reports that end-to-end inference on that card is still unverified. [Branch installation notes](https://github.com/r-krishna/RFD4-Proteina/blob/691e1f438a74aea180388264f86f6893f86bd87f/docs/installation.rst)

## 1. Check the GPU and install the tools

Open PowerShell and check that Windows can see your NVIDIA GPU.

```powershell
nvidia-smi
```

Look for your GPU name and driver version. The launcher requires a driver compatible with CUDA 13.2. If the command fails or shows no GPU, resolve the Windows driver issue before proceeding.

Install Git and uv with WinGet. Skip either command if that tool is already installed. The launcher uses uv to obtain Python 3.12.10.

```powershell
winget install --id Git.Git --exact
winget install --id astral-sh.uv --exact --scope user
```

Open a **new PowerShell window** so it picks up the updated PATH. Verify that both commands print a version.

```powershell
git --version
uv --version
```

## 2. Clone the Windows branch

From a writable folder where you want to keep the repository, run the following commands. The destination is named `RFD4-Proteina-windows` so it can sit alongside an existing upstream checkout.

```powershell
git clone --depth 1 --branch experimental/windows-cu132 https://github.com/r-krishna/RFD4-Proteina.git RFD4-Proteina-windows
cd RFD4-Proteina-windows
git branch --show-current
Test-Path .\scripts\windows-gpu.cmd
```

The last two commands should print `experimental/windows-cu132` and `True`. Stay in this folder for the remaining steps. The Windows changes are already in this branch.

## 3. Install and run the first inference

Run the launcher with no arguments. Its default action installs the environment and runs one inference example. The first run needs internet access for Python, packages, and the Hugging Face model bundle (about 1.7 GB for the bundle alone).

```powershell
.\scripts\windows-gpu.cmd
$LASTEXITCODE
```

The launcher creates or reuses `.venv-windows-gpu`, installs the CUDA 13.2 PyTorch build, checks the dependencies, and executes a small tensor operation on the GPU. Once those checks pass, it runs one replicate of the bundled `data/benchmarks/monomer/monomer-short.cif.zst` input. You can leave the virtual environment activation to the launcher, which calls its executables directly. [Windows launcher source](https://github.com/r-krishna/RFD4-Proteina/blob/691e1f438a74aea180388264f86f6893f86bd87f/scripts/windows-gpu.cmd)

## 4. Find the generated structure

**Success means the launcher prints an absolute path to a nonempty `.cif` file and PowerShell reports exit code `0`.** The GPU name appears earlier, during the device check. Each successful example run writes its structure beneath `runs\windows-smoke-<unique-id>\inference_samples\`. To list the structures from these runs, newest first, use:

```powershell
Get-ChildItem -Path '.\runs\windows-smoke-*\inference_samples' -Recurse -File -Filter '*.cif' |
    Sort-Object LastWriteTime -Descending |
    Select-Object LastWriteTime, Length, FullName
```

Rerun the same launcher to repeat the example. It reuses the environment and cached downloads and chooses a fresh output folder. The default Windows run skips scoring metrics.

## If the run stops

| What you see | What to check |
| --- | --- |
| PowerShell cannot find `git` or `uv` | Reopen PowerShell after installing the tool and rerun its version check. |
| PowerShell cannot find `windows-gpu.cmd` | Repeat the branch and path checks in step 2 from the cloned folder. |
| The dependency or CUDA check fails | Read the first error above the exit code. For a CUDA error, record the GPU name and driver version from `nvidia-smi`. |
| The GPU name prints, then a model download fails | Check the download error and connectivity to Hugging Face. After resolving it, rerun the launcher from the same checkout. |

For a reproducible issue report, include the first failing command or traceback and the output of `git rev-parse HEAD`. This guide was checked against commit `691e1f438a74aea180388264f86f6893f86bd87f`.
