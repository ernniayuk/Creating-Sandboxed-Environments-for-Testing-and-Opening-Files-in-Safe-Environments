# Creating-Sandboxed-Environments-for-Testing-and-Opening-Files-in-Safe-Environments
This project focused on building isolated, disposable sandboxes for safely executing untrusted binaries, email attachments, and macro-enabled documents.
Three types of environments were provisioned: lightweight containers (Docker with read-only root filesystems), full virtual machines (VMware Workstation with snapshots), and Windows Sandbox (native Hyper-V). Network access was restricted via a virtual bridge to an internal-only VLAN with no internet egress, and inbound connectivity was disabled using iptables and Windows Defender Firewall rules.

A standardized workflow was developed: incoming suspicious files are automatically hashed (SHA-256), copied into the sandbox via shared folders (mounted as read-only), and executed under monitoring tools (Sysinternals ProcMon, Wireshark, and strace). Behavioral artifacts — registry changes, file creations, network connection attempts — are logged and then the sandbox is reverted to a clean snapshot. This environment enabled safe analysis of three confirmed ransomware samples and five phishing documents without any risk to production hosts.

- Configured Docker sandbox with --read-only --tmpfs /tmp --network none --cap-drop=ALL to run untrusted ELF binaries.
 -Deployed Windows Sandbox using a custom .wsb configuration file disabling clipboard, printer redirection, and microphone access.
- Used VMware linked clones to spin up a fresh Windows 10 sandbox in under 2 minutes; reverted daily via vmrun revertToSnapshot.
- Implemented file pre-scanning script: calculates hash, checks VirusTotal API (if score > 5/70), then auto-copies to sandbox queue.
- Monitored a malicious Office document using ProcMon: detected reg.exe adding a Run key — blocked via sandbox network isolation.
