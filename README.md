# Virtual PTX Evolved on Cisco Modeling Labs

## Scope
This node defintion is intended for the freely available Juniper Networks product referred to as "vJunos Evolved for Labs".  While there are several virtual editions of different Junos-based products, this CML node defintion is intended to work with only one variant:

- Linux-only variant of the base Junos operating system (not FreeBSD based builds or products without the "Evolved" designation)
- Single virtual machine form factor (some prior editions were dual virtual machine -- one for the Routing Engine [RE] and one for the Packet Forwarding Engine [PFE])
- No nested virtualization (VM inside of a VM, as is the case with some variants)  

Currently, the images can be downloaded without an active support account by accepting a license agreement here:  https://www.juniper.net/us/en/dm/vjunos-labs.html.

This image is a virtualized variant of the PTX router / MPLS switch control plane and forwarding engine.  It may be possible to adapt this node defintion for other variants that are similar to the description above.

## Technical Details

Juniper Networks provides a KVM QCOW2 virtual disk image and KVM Domain XML configuration example for the defintion of the virtual machine.  These instructions are based on Junos version 23.2R1-S1.  Please refer to the latest XML configuration example when creating your CML node defintion.

The difference between this Junos virtual router and many other VNFs that operate as-is under CML are:

- Junos expects specific SMBIOS parameters to be passed from the hypervisor to the base Linux operating system for the product to boot properly.  Upon boot, the Junos base operating system will run a shell script that checks for various parameters before starting its services and management CLI.  One of these parameters is an assembly ID that will be null (0x0000) by default, if the SMBIOS arguments are not defined correctly.  In this case, the router will boot to an error message stating the platform is unsupported.  SMBIOS arguments will require explict configuration within CML.
  
- The release notes detail certain minimum CPU requirements for the product to operate correctly.  Not meeting these requirements may prevent the product from booting or properly forwarding traffic:
  - While the release notes call for Intel Ivy Bridge or later processors, this example was successfully tested using an older Sandy Bridge processor.  You may need to explictly identify your CPU (cat /proc/cpuinfo) and define the cpu_model in CML, as was the case with a Westmere host that was tested.
  - The Intel vmx CPU flag is required for proper operation and must be presented to the virtual machine.  This may require explict configuration within CML, based on how the CPU model is defined.



