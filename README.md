# Virtual PTX Evolved on Cisco Modeling Labs

## Scope
This node definition is intended for the Juniper Networks product referred to as "vJunos Evolved for Labs".  While there are several virtual editions of Junos-based products, this CML node definition is intended to work with this variant:

- Linux-only edition of the base Junos operating system (not for FreeBSD-based builds or products without the "Evolved" designation)
- Single virtual machine form factor (some editions are dual virtual machine -- one for the Routing Engine [RE] and one for the Packet Forwarding Engine [PFE])
- No nested virtualization (VM inside of a VM, as is the case with some variants)  

Currently, the images can be downloaded here:  https://www.juniper.net/us/en/dm/vjunos-labs.html.

This image is a virtualized variant of the PTX router / MPLS switch control plane and forwarding engine.  

## Technical Details

Juniper Networks provides a KVM (libvirt) QCOW2 virtual disk image and Domain XML configuration example for the definition of the virtual machine.  These instructions are based on Junos version 23.2R1-S1.  Please refer to the latest XML configuration example when creating your CML node definition.

The difference between this Junos virtual router and many other VNFs that operate as-is under CML are:

- Junos expects specific SMBIOS parameters to be passed from the hypervisor to the base Linux operating system for the product to boot properly.  Upon boot, the Junos base operating system will run a shell script that checks for certain parameters before starting its services and management CLI.  One of these parameters is an assembly ID that will be null (0x0000) by default, if the SMBIOS arguments are not defined correctly.  In this case, the router will boot to an error message stating the platform is unsupported.  

- The release notes detail certain minimum CPU requirements for the product to operate correctly.  Ignoring these requirements may prevent the product from booting or properly forwarding traffic:
  - While the release notes call for Intel Ivy Bridge or later processors, this node definition was successfully tested using an older Sandy Bridge processor.  
  - You may need to explicitly identify your CPU (from the Junos Linux shell:  ```cat /proc/cpuinfo```) and define the ```cpu_model``` in CML, as was the case with a Westmere host that was tested.
  - The Intel ```vmx``` CPU flag is required for proper operation and must be presented to the virtual machine.  This may or may not require explicit configuration within CML, based on a number of hardware-specific factors.  Examining the output of ```cat /proc/cpuinfo``` from the Junos Linux shell will reveal which CPU flags are passed from the hypervisor.  

### SMBIOS Parameters

Based on the Junos 23.2R1-S1 libvirt domain XML example, the required SMBIOS arguments are:

```
<ns0:commandline>
  <ns0:arg value="-smbios"/>
  <ns0:arg value="type=0,vendor=Bochs,version=Bochs"/>
  <ns0:arg value="-smbios"/>
  <ns0:arg value="type=3,manufacturer=Bochs"/>
  <ns0:arg value="-smbios"/>
  <ns0:arg value="type=1,manufacturer=Bochs,product=Bochs,serial=chassis_no=0:slot=0:type=1:assembly_id=0x0D20:platform=251:master=0:channelized=no"/>
</ns0:commandline>
```
- Type 0 refers to "bios" values
- Type 1 refers to "system" values
- Type 3 refers to "chassis" values

This translates to the following individual SMBIOS parameters, as they will be defined in the CML node definition:

```
bios.vendor = Bochs
bios.version = Bochs
chassis.manufacturer = Bochs
system.manufacturer
system.product = Bochs
system.serial = chassis_no=0:slot=0:type=1:assembly_id=0x0D20:platform=251:master=0:channelized=no
```
### CML Node Definition Details

todo - include web UI screen caps that reflect SMBIOS configuration and use of cpu_model

### CML Disk Image Definition Details

todo

### Automation

todo:  detail the use of the Junos config.qcow2 image for the default configuration and other CML specific automation functions that would help bring up this router for automated test scenarios.
