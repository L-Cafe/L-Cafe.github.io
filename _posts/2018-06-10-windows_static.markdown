---
title:      "Static analysis tools for Windows"
date:       2018-06-10 12:08:50 +0100
tags:       Microsoft software-rev static-analysis
category:   Cybersecurity
comment:    header sizes are 2000x750
header:
  image:	/assets/images/2018-06-10-windows_static/header.jpg
  teaser:	/assets/images/2018-06-10-windows_static/header.jpg
  caption:	"Photo credit: [**Unsplash**](https://unsplash.com)"
---

I'm currently studying malware analysis, so I wanted to write a post on the tools I've been using recently, to serve as a reference for me and hopefully for anyone interested in binary reversing.

Some tools will have functionality that overlaps with other of the listed tools. It's up to you which one to use.

# Free tools

Either fully open source, or freeware (unrestricted, so no shareware).

## [Dependency Walker](http://dependencywalker.com/)

Displays a hierarchical tree diagram of all dependent module, with exported, and imported functions.

## [FileAlyzer](https://www.safer-networking.org/products/filealyzer/)

Application with functionality to show resources, PE headers, hex dumps, INI contents, and more. It can read UPX compressed files, ELF (Linux) as well as regular DLL and EXE files.

## [HxD](https://mh-nexus.de/en/hxd/)

Hexadecimal editor. Everyone has their own favourite, but this one works well and is free.

## [Malcode Analyst Pack](http://www.sandsprite.com/iDef/MAP/)

Pack containing lots of useful software and added functionality to aid in static analysis. Among other utilities, it installs shell extensions (that appear upon right-clicking files and folders) for computing MD5 tools, or showing the strings on a file.

## [PEBrowse](http://www.smidgeonsoft.prohosting.com/pebrowse-pro-file-viewer.html)

Windows disassembler for 64 and 32 bit programs. Presents the PE structure, with its headers, imports, exports, debug symbols and more in an intuitive GUI. Comes with helpful tools such as hex/ASCII equivalents, window messages and error codes. It also displays metadata for .NET assemblies.

## PEiD

Detects common packers, cryptors and compilers for PE files.

The official website seems to have been discontinued, but you can download it [here]({{site.url}}/assets/other/PEiD-0.95-20081103.zip).

## [pev](http://pev.sourceforge.net/)

Command-line open source tool to analyse PE files. Runs on Windows, macOS and Linux.

## [PEView](http://wjradburn.com/software/)

Shows the structure of a PE and COFF file, dispalying headers, sections, import/export tables, and resource information in file types such as EXE and DLL.

## [Resource Hacker](http://angusj.com/resourcehacker/)

Shows resources, such as images, dialogs, executables and binaries embedded in PE files such as EXE and DLL.

## [Radare2](http://www.radare.org/r/)

Better known for its dynamic analysis capabilities, it is also capable of performing binary diffing, extract relocation tables, and of course disassemble binaries written in multiple languages for many different architectures.

## [Sysinternals Suite](https://docs.microsoft.com/en-us/sysinternals/downloads/sysinternals-suite)

Not exactly a tool, but a collection of tools, as the name implies.

Here are a few interesting ones:

- **Sigcheck**: Shows file version number, timestamp and digital signature details, and optionally allows for VirusTotal sample uploads.
- **Strings**: Shows hardcoded strings in an executable file.
- **Whois**: Similar to the Unix tool, shows details on a given Internet address. Maybe you want to investigate a certain string pointing to a domain?

## [UPX](https://upx.github.io/)

Very popular executable packer used by malware authors. You can unpack some UPX-packed executables by supplying the command-line option `-d` to the `upx` tool.

# Paid tools

## [PE Explorer](http://heaventools.com/)

Resource editor, UPX unpacker, disassembler, dependency scanner and more.

## [PEStudio](https://winitor.com/)

PEStudio implements very useful features aimed to help detecting anomalies and suspicious patterns. It ships with VirusTotal functionality that queries the service for MD5 hashes of a file. It also list imports and resources. Ships with both a GUI and CLI versions.
