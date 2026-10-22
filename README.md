```text
Title: FinalWire AIDA64 kerneld-x64.sys Intel BAR copy to caller pointer

Vendor: FinalWire Kft.
Product: AIDA64 Extreme
Component: kerneld-x64.sys
SHA-256: 197970c3ff04b88cb1058730b51bc78e73643f8047051fe469cceed3dbf5a7ec
Size: 81296 bytes
PE timestamp: 2025-07-10 09:51:06 UTC
Version resource: none
Signer: CN=FinalWire Kft., thumbprint 948F306B529D54986EFDF8B33A5F02EC73A4476D
Device: \\.\AIDA64Driver

Description:
kerneld-x64.sys IOCTL 0x80112138 maps an Intel PCI memory BAR with MmMapIoSpace and copies that range to a caller-supplied pointer. The caller chooses the PCI device, the copy length, and the destination. The source bytes come from the device BAR, not from the caller. The mapped address is that BAR plus a short offset, not an arbitrary physical page.

Access:
Tested on 2026-10-22 on an isolated Windows 10 virtual machine. An administrator loaded the driver (service AidaX64). Local account aida-low, which is not in Administrators, then opened \\.\AIDA64Driver and sent IOCTL 0x80112138. The device security descriptor grants Everyone (WD) access mask 0x1201bf:

O:BAG:SYD:(A;;0x1201bf;;;WD)(A;;FA;;;SY)(A;;FA;;;BA)(A;;0x1200a9;;;RC)S:AI(ML;;NW;;;LW)

Loading the driver still requires an administrator. Sending this IOCTL does not.

Read-back:
On 2026-10-22 the same Intel device pci 3:0.0, configuration register 0x10, was used twice. Two caller buffers were filled with 0xA5. The first call copied 16 bytes into buffer A. The second call copied 32 bytes into buffer B. A changed only in the first 16 bytes (starting 48 02 10 40) and stayed 0xA5 after that. B matched A in the first 16 bytes, changed in bytes 16-31, and stayed 0xA5 after byte 32.

This note covers that file and IOCTL 0x80112138. The same file also has IOCTLs 0x8011213c, 0x80112140, and 0x8011214c, which pin the device instead of taking it from the caller. CVE-2024-26507 identifies IOCTL 0x80112104 and SHA-256 7790ab2136def33afc6c86d64da81c2e1bc25d34e33282c987961cdaa87e4330. CVE-2019-7244 is IOCTL 0x80112084 on kerneld-v64.sys, not this file.

The installer version that contained this exact file was not recorded by this test.

Suggested fix: reject these IOCTLs, or limit the mapped range to the selected BAR and copy only into a probed user buffer.

CVE:
```
