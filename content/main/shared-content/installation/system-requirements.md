---
uid: SystemRequirements
---

# System requirements

[!include[product](../_includes/inline/product-name.md)] is supported on a variety of platforms and processors. Install kits are available for the following platforms:

| Operating System | Platform | Installation Kit | Processor(s) |
|-------------------|-------------|----------------------------------|-------------|
<<<<<<< HEAD
<<<<<<< HEAD
| Windows 10 Enterprise <br> Windows 10 IoT Enterprise <br> Windows 11 Enterprise <br> Windows Server 2019 <br> Windows Server 2022 | x64 | <code>[!include[installer](../_includes/inline/installer-name.md)]-x64_.msi</code>     | Intel/AMD 64-bit processors |
| Debian 10, 11<br>Ubuntu 20.04, 22.04 | x64 | <code>[!include[installer](../_includes/inline/installer-name.md)]-x64_.deb</code>     | Intel/AMD 64-bit processors |
| Debian 10, 11<br>Ubuntu 20.04, 22.04 | ARM32 | <code>[!include[installer](../_includes/inline/installer-name.md)]-arm_.deb</code>  | ARM 32-bit processors |
| Debian 10, 11<br>Ubuntu 20.04, 22.04 | ARM64 | <code>[!include[installer](../_includes/inline/installer-name.md)]-arm64_.deb</code>  | ARM 64-bit processors |
=======
| Windows 10 Enterprise <br>Windows 10 IoT Enterprise | x64 | <code>[!include[installer](../_includes/inline/installer-name.md)]-x64_.msi</code>     | Intel/AMD 64-bit processors |
| Debian 9, 10<br>Ubuntu 18.04, 20.04 | x64 | <code>[!include[installer](../_includes/inline/installer-name.md)]-x64_.deb</code>     | Intel/AMD 64-bit processors |
| Debian 9, 10<br>Ubuntu 20.04 | ARM32 | <code>[!include[installer](../_includes/inline/installer-name.md)]-arm_.deb</code>  | ARM 32-bit processors |
| Debian 10<br>Ubuntu 20.04 | ARM64 | <code>[!include[installer](../_includes/inline/installer-name.md)]-arm64_.deb</code>  | ARM 64-bit processors |
>>>>>>> parent of fe84c03 (Merge pull request #5 from osisoft/main)
=======
| Windows 10 Enterprise <br> Windows 10 IoT Enterprise <br> Windows 11 Enterprise <br> Windows Server 2019 <br> Windows Server 2022 | x64 | <code>[!include[installer](../_includes/inline/installer-name.md)]-x64_.msi</code>     | Intel/AMD 64-bit processors |
| Debian 10, 11 <br> Ubuntu 20.04, 22.04 | x64 | <code>[!include[installer](../_includes/inline/installer-name.md)]-x64_.deb</code>     | Intel/AMD 64-bit processors |
| Debian 10, 11 <br> Ubuntu 20.04, 22.04 | ARM32 | <code>[!include[installer](../_includes/inline/installer-name.md)]-arm_.deb</code>  | ARM 32-bit processors |
| Debian 10, 11 <br> Ubuntu 20.04, 22.04 | ARM64 | <code>[!include[installer](../_includes/inline/installer-name.md)]-arm64_.deb</code>  | ARM 64-bit processors |
>>>>>>> 6e64e0fc7b4fd589ee7219791ac8da0fdf5a2306

Alternatively, you can use tar.gz files with binaries to build your own custom installers or containers for Linux. For more information on installing the adapter with Docker containers, see <xref:InstallUsingDocker>.

## PI Web API compatibility

This version of [!include[product](../_includes/inline/product-name.md)] is compatible with PI Web API 2021 and later. 

<<<<<<< HEAD
## PI Adapter for OPC UA upgrade

<<<<<<< HEAD
If you are upgrading AVEVA Adapter for OPC UA from version 1.1 to 1.3 or newer and are sending data to PI Web API 2019, AVEVA Data Hub, or both, read [OPC UA Adapter - Upgrade from v1.1 to v1.2](https://osisoft.lightning.force.com/lightning/r/Knowledge__kav/ka08W000000frOFQAY/view). This knowledge article contains important information about upgrade scenarios.
=======
If you are upgrading PI Adapter for OPC UA from version 1.1 to 1.3 and are sending data to PI Web API 2019, OCS, or both, read [OPC UA Adapter - Upgrade from v1.1 to v1.3](https://osisoft.lightning.force.com/lightning/r/Knowledge__kav/ka08W000000frOFQAY/view). This knowledge article contains important information about upgrade scenarios.
>>>>>>> parent of fe84c03 (Merge pull request #5 from osisoft/main)
=======
## Performance Metrics

A wide range of Linux and Windows based devices support AVEVA Adapters, from small single board computers to large servers. Stream count and event throughput vary based on the device's hardware specification. 

Performance guidelines for AVEVA Adapters when running on typical small and large devices are as follows:

- Small Devices - 1 Core CPU, 512 MB RAM. 1k events/sec, 1k streams total
- Large Devices - 2 Core CPU, 8 GB RAM. 20k events/sec, 20k streams total

Actual performance of AVEVA Adapters vary depending on different factors, such as the performance of data source, network latency, and hardware resource consumption by other applications on the device. We recommend solid state storage on the device to improve the performance, robustness, and reliability of the AVEVA Adapter data buffering mechanism. 
>>>>>>> 6e64e0fc7b4fd589ee7219791ac8da0fdf5a2306
