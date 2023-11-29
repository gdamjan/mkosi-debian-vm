# Notes

Use [`mkosi`](https://github.com/systemd/mkosi/) to build a Debian based VM image, to boot on AWS.

- Used `mkosi` from git (or later than v19).
- Used a tools distro of Fedora Rawhide for systemd 255 tooling required for `mkosi burn`
- Target OS is Debian bookworm (12)
- Creates a GPT disk with an EFI Systems Partition (ESP) and boots with systemd-boot

Testing it on AWS with AmazonLinux2023 as the hose (which is why I need a ToolsDistribution)

Reference:
https://github.com/systemd/mkosi/blob/main/mkosi/resources/mkosi.md