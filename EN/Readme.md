- [PURPOSE](#purpose)
- [DEFINITIONS](#definitions)
- [GPO AND INTUNE](#gpo-and-intune)
- [LIST OF RECOMMANDATIONS](#list-of-recommandations)
  - [GENERIC RECOMMANDATIONS](#generic-recommandations)
    - [PASSWORDS](#passwords)
    - [BOOT ORDER](#boot-order)
    - [ACTIVATION OF DEVICES](#activation-of-devices)
    - [SECURE BOOT](#secure-boot)
  - [PROTECTIONS](#protections)
    - [AMD XD / INTEL NX](#amd-xd--intel-nx)
    - [INTEL SGX](#intel-sgx)
    - [TPM](#tpm)
      - [TPM 2.0](#tpm-20)
    - [PERFORMANCE](#performance)
  - [VIRTUALISATION](#virtualisation)
  - [WAKE UP](#wake-up)
    - [WAKE ON LAN](#wake-on-lan)
    - [WAKE ON TIMER](#wake-on-timer)
  - [NETWORK COMMUNICATION](#network-communication)
    - [WIRELESS](#wireless)
    - [NETWORK INTERFACE CONTROLLER (NIC)](#network-interface-controller-nic)
  - [CONFIGURATION OF EQUIPMENT](#configuration-of-equipment)
    - [SATA OPERATION](#sata-operation)
    - [USB](#usb)
  - [HEALTHCHECK](#healthcheck)
    - [SMART](#smart)
  - [UPDATE AND DOWNGRADE](#update-and-downgrade)
    - [UPGRADE/DOWNGRADE PROCESS](#upgradedowngrade-process)
    - [UEFI ENCAPSULATION](#uefi-encapsulation)
  - [COMPUTRACE](#computrace)

# PURPOSE
This document aims to provide you with information about hardening the BIOS of your workstations.
Each setting must be adapted to your needs and to the population of your computer.

* In the rest of the document, the term BIOS will be used to refer to both the BIOS and the UEFI.
* The analysis was done on a Dell workstation, some terms or options may vary or not be available on your workstation.
* The analysis focuses on the options that have an impact on the security of the computer.

# DEFINITIONS
* BIOS: The BIOS (Basic Input/Output System) is a software program that controls the basic functions of a computer. It manages the interactions between the operating system, peripherals and hardware components of the computer. The BIOS can be considered the keystone of the entire system, as it is responsible for the initial start-up of the operating system.
* UEFI: The UEFI standard (Unified Extensible Firmware Interface) defines an interface between the firmware and the operating system of a computer. On some motherboards, this interface replaces the BIOS.

![BIOS](./FR/Images/BIOS.png)
![UEFI](FR/Images/UEFI.png)

# GPO AND INTUNE
If your company has an SOC, it is possible to use tools provided by the manufacturers.
This is the case for computers from Dell, HP and Lenovo.
_If you are considering renewing your computers, this may be a point to take into account._

Pour Dell : 
* Dell Command PowerShell : https://www.dell.com/support/kbdoc/fr-fr/000177240/dell-command-powershell-provider
* Dell BIOS Provider : https://www.powershellgallery.com/packages/DellBIOSProvider/2.1.0 

Pour HP : 
* https://www.powershellgallery.com/packages/HPEBIOSCmdlets/3.0.0.0

Pour Lenovo : 
* https://support.lenovo.com/us/en/solutions/ht100612-lenovo-bios-setup-using-windows-management-instrumentation-deployment-guide-thinkpad
* https://download.lenovo.com/pccbbs/thinkcentre_pdf/mvdeploy_en.pdf 

# LIST OF RECOMMANDATIONS
## GENERIC RECOMMANDATIONS
### PASSWORDS
It is important to set a password to access the BIOS configuration to ensure that only authorized personnel can view and change the settings.

If an attacker can gain unauthorized access to the BIOS, he can modify its settings to compromise the system.
For example, they can configure the BIOS to boot from a malicious device or change settings that disable important system security features.

That is why it is recommended to: 
* Set up a strong password,
* Avoid using the same password for your entire fleet of terminals,
* Take into account that mobile devices are more subject to loss / theft when defining the password.
### BOOT ORDER
The boot order identifies to the computer the devices that might be allowed. The computer will search each identified device sequentially until it finds an active bootable partition. At that point, the computer will attempt to boot the system or utility found on the first available device.

In many cases, booting to the hard drive is the last option to allow for a repair boot via USB or PXE.

Here are some examples of the dangers of allowing the choice of boot order:
* Malware installation or means of persistence: the attacker can deploy malware on the computer via a boot on another drive. This software can cause significant damage to the company's entire computer system during the next normal startup (e.g., dropping a malicious file, modifying the registry key, etc.).
* Data theft: the attacker boots from a removable disk to copy confidential company data and take it with him.

Therefore, it is recommended that: 
* Only the partition on which the system is installed should be booted.
### ACTIVATION OF DEVICES
Most modern computers allow the BIOS administrator to determine exactly which device features to expose to the operating system and, therefore, to the user.

For example, via this option it is possible to disable the following devices: 
* IDE/SATA,
* USB / SD card,
* Network devices (Wi-Fi, Bluetooth, NFS, GPS, modem, etc.)
* Microphone / camera

This is why it is recommended to:
* Only let the equipment that is necessary and supported by your company policy be activated.
  * Keep in mind that reactivation will require going into the BIOS and therefore a standard user will not be able to do it.
### SECURE BOOT
In theory this option is always enabled on newer workstations, as it is necessary for a UEFI boot.

Secure Boot is a BIOS/UEFI feature that ensures that a computer boots :
* using only software approved by the computer manufacturer, 
* has not been altered since its creation,

That is why it is recommended: 
* Leave it enabled,
* Keep the BIOS/UEFI updated to keep the signatures up to date.
## PROTECTIONS
### AMD XD / INTEL NX
Both Intel and AMD have taken steps to try to provide better protection against exploits with the Intel Execute Disable (XD) and AMD Enhanced Virus Protection (NX-bit) options.

These technologies allow the operating system to mark pages of memory as non-executable. This capability prevents certain buffer overflows by preventing instructions from being executed on the marked memory pages.

Although it does not provide complete protection against malware, it is an additional layer of security.

Therefore, it is recommended to: 
* Activate this option as soon as possible.
### INTEL SGX
SGX enclaves are protected and encrypted memory spaces that allow applications to run in a secure mode isolated from the rest of the operating system. SGX enclaves can be used to run mission-critical operations such as password storage, financial transactions, artificial intelligence and machine learning, or other tasks that require high security.

Therefore, it is recommended to: 
* Activate this option as soon as possible,
* Increase the memory allocated to this space (max 128Mb)
* Make sure that your developers take into account this specificity as soon as possible.
### TPM
The Trusted Computing Group defines the TPM as a computer chip that can securely store the artifacts used to authenticate the platform.

If an organization is considering advanced boot integrity measures or full disk encryption, the TPM and the implications of its use should be studied in detail. The Trusted Computing Group website provides a wealth of information on the applications supported by TPM.

Documentation : https://trustedcomputinggroup.org/work-groups/trusted-platform-module/ 

Therefore, it is recommended to: 
* Activate TPM,
  * Enable TPM 2.0
* Check that the software security equipment uses this chip (e.g. Bitlocker, NAC)
#### TPM 2.0
TPM 2.0 makes the TPM chip visible to the operating system.

There are several possible options for this configuration:
* Clear: This setting clears the owner information of the TPM module and the TPM module is enabled after clearing.
* PPI Bypass for Enable Commands: This is a feature that allows you to bypass the BIOS user interface to enable TPM commands. It can be useful to automate security processes without human intervention.
* PPI Bypass for Disable Commands: This is a feature that allows you to bypass the BIOS user interface to disable TPM commands. It can be useful to prevent malicious users from disabling the TPM chip.
* Key Storage Enable: This feature allows encryption keys to be stored in the TPM chip for added security. The keys can be used to protect sensitive data, such as passwords, encrypted files, etc.
* Tenable Attestation: This feature verifies the integrity of the system using the TPM chip. It can be used to ensure that the system has not been compromised or altered in any way.
* SHA-256: This is a cryptographic hash function that is used to secure data by creating a unique digital fingerprint of each file or document. It is used in security processes such as digital signature, file integrity verification, etc.
* PPI Bypass for Clear Commands: This is a feature that allows you to bypass the BIOS user interface to clear the data stored in the TPM chip. It can be used to erase sensitive data in case of system compromise or loss.

Knowing this it is recommended to: 
* Disable all bypass options
* Enable SHA256 (or more if available)
### PERFORMANCE
Intel provides several options in its performance section.

* MultiCore support: this field indicates whether only one core of the processor should be activated or all of them.
* Intel SpeedStep: this field indicates whether the Intel SpeetStep mode should be active or not for the processor.
  * Intel SpeedStep is a technology from Intel that reduces the power consumption of processors by dynamically adjusting their clock frequency according to the workload. This allows processors to run more efficiently by saving power and reducing heat generation.
* C-states: This option enables or disables additional processor sleep states.
  * C-states are a feature of modern processors that reduce power consumption by putting parts of the processor to sleep when not in use.
* Intel Turbo Boost: This field indicates whether or not the Intel TurboBoost mode should be enabled on the processor.
  * Turbo Boost is an Intel technology that increases the clock speed of processors when they are operating below their maximum capacity. This allows processors to deliver extra performance when it is needed, while saving power when it is not.
* HyperThread Control: This field indicates whether or not HyperThreat mode should be enabled.
  * Instead of giving a large workload to a single core, threaded programs divide the work into several software tasks (threads). These tasks are processed in parallel by different processor cores to save time.

Knowing this it is recommended: 
* Activate multicore,
* Enable Speedstep, C-States, Turboboost.
  * The latter are however fallible via attack techniques such as measuring power consumption to extract sensitive information from the chip, such as encryption keys. Nevertheless, these attacks are very sophisticated and require physical access to the computer.
* Enabling or disabling HyperThread will depend on how you feel. Many vulnerabilities are discovered on it, patches have impacts on the performance of this technology and for a large part of the users its deactivation will be transparent.
## VIRTUALISATION
Virtualization technology in the BIOS also improves virtual machine performance by providing direct access to hardware resources, which reduces the cost of processing virtual machine access requests.

Therefore, it is recommended to: 
* Enable this option only on virtualization devices
  * If this option is enabled, it is recommended to apply all available protections such as SMM as long as they have no impact on production.
## WAKE UP
### WAKE ON LAN
The Wake-On-LAN function is used to wake a computer from its shutdown or sleep state in order to start the computer's operating system and enable network activity.

Some systems, such as SCCM, can use it to wake up computers to install patches at set times.

However, Wake-on-LAN has many risks:
However, Wake-on-LAN has n* Bounce attacks: attackers can use Wake-on-LAN to bounce their attack to a target computer through a third-party computer, making the origin of the attack difficult to locate.
* Replay attacks: attackers can capture data packets sent to wake up a computer and replay them later to wake up the computer without authorization.
* Denial of service attacks: attackers can send multiple Wake-on-LAN packets to a computer to wake it up continuously, which can lead to network overload and service interruption.
* Packet injection attacks: attackers can inject malicious Wake-on-LAN packets that can execute malicious code on the target computer.
* Information leakage: If Wake-on-LAN packets contain sensitive information, such as passwords, they can be intercepted and compromise the security of the target computer.
* Broadcast use: these packets can be filtered by routers (which limit broadcast domains) and can prevent the Wake-on-LAN from working properly.udges.

Therefore, it is recommended to: 
* Disable this option if no tool uses it
### WAKE ON TIMER
When a computer model does not support Wake On LAN, this feature can be used as a stopgap measure to ensure that patch management systems can contact the computer more reliably by booting the workstation at a set time.

For this reason, it is recommended to: 
* Disable this option if no tool uses it
## NETWORK COMMUNICATION
### WIRELESS
Wireless switching prevents the network from being connected at the hardware level. Once the link is detected on the wired network adapter, the wireless adapter is automatically disabled.

This feature is an excellent measure to:
* Prevent unauthorized network bridging: network bridging is a technique used to bypass security controls by establishing a connection between two different networks. Disabling wireless connectivity can prevent users from establishing unauthorized network bridges by automatically disabling the wireless connection when the wired connection is detected.
* Limiting the risk of network security compromise by preventing users from establishing unauthorized network bridges, disabling wireless connectivity can help limit the risk of network security compromise by preventing potential attackers from accessing sensitive data or launching man-in-the-middle attacks from their own machine.

Therefore, it is recommended to: 
* Enable this option.
### NETWORK INTERFACE CONTROLLER (NIC)
In the BIOSes of Dell computers, the "Integrated NIC" feature allows you to control the settings of the integrated network card. For example, you can enable or disable the network card, configure speed and duplex settings (e.g. 10/100/1000 Mbps), or configure advanced settings such as VLAN support or Wake-on-LAN.

Therefore, it is recommended to: 
* Enable this option only if the workstation is to use the built-in network card,
  * Disable PXE boot if not needed
  * Enable PXE boot if used
    * Enable the protections available with UEFI on this protocol
## CONFIGURATION OF EQUIPMENT
### SATA OPERATION
This option configures the operating mode of the built-in SATA hard disk controller.

* Disabled: SATA ports are disabled and no SATA devices can be used. This option is typically used if you do not plan to use any SATA devices.
* AHCI: The SATA controller is configured in AHCI (Advanced Host Controller Interface) mode. AHCI mode is generally recommended for modern HDDs and SSDs because it allows for better performance and greater flexibility in disk management. It also supports advanced features such as NCQ (Native Command Queuing), power management and hot-swapping.
* RAID On: The SATA controller is configured to support RAID disk configurations. This option is typically used if you plan to set up a RAID system using two or more hard drives or SSDs. RAID mode allows you to create redundant disk configurations to improve fault tolerance and storage performance.

Therefore, it is recommended to: 
* Activate the AHCI mode for the majority of the park
* Activate the RAID mode only if the computer is configured to have RAID.
### USB
This field configures the Interactive USB controller.

When Interactive USB is enabled, it means that the computer's USB ports can be used to interact with the BIOS, for example to perform a BIOS update from a USB stick or to boot from a USB storage device.

_It is important to note that keyboards and mice will always be functional in the BIOS regardless of the configuration._

The "USB Configuration" section in Dell BIOSes allows you to configure settings related to the system's USB ports. The most common options are "Enable USB Boot support" and "Enable External USB port". Here are the roles of these two options:

* Enable USB Boot support: allows the system to boot from a USB device such as a USB stick or external hard drive. If this option is disabled, the system cannot be booted from a USB device.
* Enable External USB port: Enables or disables the use of external USB ports on the system. If this option is disabled, the external USB ports will not work. This can be useful for security reasons if you want to restrict access to external USB ports to prevent unauthorized users from connecting USB devices

Therefore, it is recommended to: 
* Disable the ability to boot from a USB stick,
* Disable the use of external USB ports if not used (for example if not using docking station).
## HEALTHCHECK
### SMART
The SMART test performs background checks to detect warning signs of impending hard drive failure, such as bad sectors, high error rates, abnormally high temperatures, etc. 
If problems are detected, the SMART test can alert the user to the need to replace the hard drive before a complete failure occurs, thus saving data and preventing data loss.

Although it is not purely security, it is recommended to enable this option.
## UPDATE AND DOWNGRADE
### UPGRADE/DOWNGRADE PROCESS
The BIOS is the program that manages the startup of the computer and is the first brick to be consolidated.
Updates can include: 
* Fixing bugs and security vulnerabilities
* Improvement of functionalities
* Optimization of performance
* Solving compatibility problems

Therefore, it is recommended to: 
* Implement a qualification process for each BIOS update,
* Set up a deployment policy,
* Audit the deployment of this update.
### UEFI ENCAPSULATION
This allows Windows Update to provide BIOS updates.

Depending on your update policy, you will have to define if you enable this option or not.
Beware, recent posts on the forums indicate that Windows 11 does not take this option into account.
## COMPUTRACE
Computrace is used to provide services such as: 
* Secure the data of a park of remote stations,
* Deploy always remotely updates, licenses or launch audits,
* Geolocate stolen computers,
* Produce reports about the machines,
* Recover files,
* Remotely erase documents or the entire hard drive.

This feature is very powerful. An attacker could use it to deploy a persistence device or a backdoor within the information system. A backdoor that could not be removed even by reinstalling, formatting or changing the disk or the BIOS.
Let's add that the functionality seems very light in terms of security (code injection, unencrypted protocol, no authentication, call via simple URL).

Documentation : https://www.kaspersky.com/blog/beware-of-vulnerable-anti-theft-applications/3837/  

Therefore, it is recommended to: 
* Disable this feature.