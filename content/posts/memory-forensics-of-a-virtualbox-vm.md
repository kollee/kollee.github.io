---
title: "Memory Forensics of a Virtualbox VM"
date:  2018-06-23T09:33:03+02:00
---
I use VirtualBox to triage and analyze malware. In contrast to VMWares .vmem file, VirtualBox does not use a separate file to store the current memory. Even if you take a snapshot or pause the VM, the current memory is not saved separately but stores the complete state of the VM in a ``.sav`` file.

Luckily, [Philippe Teuwen](http://wiki.yobi.be/wiki/RAM_analysis) describes another way to dump the RAM of a (running) VirtualBox VM by using the VirtualBox debugvm capabilities. This command creates a file in the [Executable and Linking Format (ELF)](https://en.wikipedia.org/wiki/Executable_and_Linkable_Format)  which you can analyzed using standard ELF tools like readelf or objdump. To ease the usage even further Philippe wrote a Volatility address space to extract the memory image from a core dump file using Volatility's imagecopy. Volatility includes this address space since version 2.3.

Now it is only a simple two-step process to get a memory dump of a VirtualBox VM:

Assuming your (running) VM is named *Win7* a core dump is created using

```
  $ vboxmanage debugvm Win7 dumpvmcore --filename=Win7.dmp
```

Now you can convert this core dump to a memory dump using Volatility's imagecopy

```
  $ python vol.py -f Win7.dmp imagecopy -O Win7.raw
```

Volatility can now ingest this file like any other memory dump.