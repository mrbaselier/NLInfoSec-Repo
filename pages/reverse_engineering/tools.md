---
layout: page
title: "# Reverse Engineering Tools"
permalink: /reverse_engineering/tools
---

To learn reverse engineering, this is the order of things you're going to need to learn to be able to progress.

First of all, find your place within the following bits of information, and proceed from there:

You'll need to understand programming language concepts. Understanding 1-3 programming languages from different [GL-standards](https://en.wikipedia.org/wiki/Programming_language_generations) is recommended.
Be comfortable with variables, functions, scopes, pointers, conditionals, various data structures (linked lists, trees and so on).
Next up: skim through the dragon book (Compilers: Principles, Techinques, and Tools) and Linkers and Loaders to understand how programs really work and how they're put together.
Then, the environment in which the programs tend to be run: The operating system and the processor. Check out the intel manual for starters.

Then the fun starts with concepts like processes, threads, virtual memory, privilege seperation, multi-tasking etc. Try reading Modern Operating Systems for the theory and Windows NT Device Driver development for the Windows side of things. Then, What Makes It Page? The Windows 7 (x64) Virtual Memory Manager is to be read.

You'll be starting to get the basics down right about now.

Windows Internals books are also recommended, combined with The Windows Kernel. This is rather Windows based however.

If you reach this point, reach out to this community, and we'll have more in store for you.

# Reverse Engineering Tools

A curated list of useful tools and resources for reverse engineering.

## File / Binary Enumeration
  * [Strings] - Default "strings" tool to get readable strings from binaries and files
  * [Flare Floss](https://github.com/mandiant/flare-floss) - The FLARE Obfuscated String Solver (FLOSS, formerly FireEye Labs Obfuscated String Solver) uses advanced static analysis techniques to automatically deobfuscate strings from malware binaries.
  * [PEMonitor](https://www.winitor.com/) - Spot artifacts of executable files in order to ease and accelerate Malware Initial Assessment.
  * [Foremost](http://foremost.sourceforge.net/) - Recover files based on their headers, footers, and internal data structures.

## Proces Analyzers
  * [SysInternals Process Monitor](https://docs.microsoft.com/en-us/sysinternals/downloads/procmon) - Process Monitor is an advanced monitoring tool for Windows that shows real-time file system, Registry and process/thread activity.
  * [API Monitor](http://www.rohitab.com/apimonitor) - API Monitor is a free software that lets you monitor and control API calls made by applications and services.
  * [CMD Watcher](https://www.kahusecurity.com/tools.html) - Watches for the CMD, PowerShell and other processes, suspends it, extracts the command line data, then optionally kills it.
  * [IONinja](https://ioninja.com/) - IO Ninja is a professional all-in-one terminal emulator, sniffer, and protocol analyzer.

## Reverse Engineering Suites
  * [GHidra](https://ghidra-sre.org/) - A software reverse engineering (SRE) suite of tools developed by NSA's Research Directorate in support of the Cybersecurity mission
  * [Cutter](https://github.com/rizinorg/cutter) - Another reverse engineering tool.

## Online analysis / Emulation / Sandboxing
  * [Game hacking](https://gamehacking.academy/about) - Learn how to do some basic game hacking.
  * [Hybrid Analysis](https://www.hybrid-analysis.com) - A free malware analysis service for the community that detects and analyzes unknown threats using a unique Hybrid Analysis technology.

## Offline analysis / Emulation / Sandboxing
  * [SpeakEasy](https://github.com/mandiant/speakeasy) - Speakeasy is a portable, modular, binary emulator designed to emulate Windows kernel and user mode malware.

## .NET Decompilers
  * [ILSpy](https://github.com/icsharpcode/ILSpy) - ILSpy is a .NET assembly browser and decompiler.
  * [dnSpy](https://github.com/dnSpy/dnSpy) - dnSpy is a debugger and .NET assembly editor.

## Interesting Articles
  * [Hybrid Analysis](https://www.netspi.com/blog/technical/web-application-penetration-testing/magic-bytes-identifying-common-file-formats-at-a-glance/) - Magic Bytes – Identifying Common File Formats at a Glance

## Beginners Tutorials
  * [Basic course](https://malwareunicorn.org/workshops/re101.html#0) - Basic course in reverse engineering
  * [Reverse engineering CTF's](https://osandamalith.com/2019/02/11/linux-reverse-engineering-ctfs-for-beginners/) - Focus on linux.
  * [Basic assembly concepts](https://medium.com/ax1al/dos-assembly-101-4c3660957d25) - Basic assembly

## Blogs
  * [A somewhat interesting blog](https://revers.engineering/) - A research blog

## List of resources
  * [Collection of resources for linux reverse engineering](https://github.com/michalmalik/linux-re-101) - Not curated by us.
  * [Intel manual](https://software.intel.com/content/www/us/en/develop/articles/intel-sdm.html) - If you want to reverse engineer, you're going to have to glance through this manual at some point.
