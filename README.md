# Notes

Use [`mkosi`](https://github.com/systemd/mkosi/) to build a Debian based VM image, to boot on AWS.

- Used `mkosi` from git (or later than v19).
- Used a tools distro of Fedora Rawhide for systemd 255 tooling required for `mkosi burn`
- Target OS is Debian bookworm (12)
- Creates a GPT disk with an EFI Systems Partition (ESP) and boots with systemd-boot

Testing it on AWS with AmazonLinux2023 as the host (which is why I need a ToolsDistribution)

```
mkosi --debug-shell burn /dev/nvme1n1
```
> `/dev/nvme1n1` is a second volume attached to the Ec2 Instance, tested with a 8GB Volume.

Next I powerdown the Ec2, detach both volumes, and re-attach the new volume as `/dev/xvda`,
thus making it the new boot drive. On start, the Ec2 will boot in the newly installed Debian.

Alternatively, we can just create an AMI from the Volume.

```
aws ec2 create-snapshot \
    --volume-id vol-1234567890abcdef0 \
    --description 'Snapshot for AMI' \
    --tag-specifications 'ResourceType=snapshot,Tags=[]'

aws ec2 register-image \
    --name my-image \
    --root-device-name /dev/xvda \
    --block-device-mappings DeviceName=/dev/xvda,Ebs={SnapshotId=snap-1234567890abcdef0}
```

Alternatively, alternatively, use with Packer and its EBS-Surrogate builder to make the AMI automatically.

## Reference:

- https://github.com/systemd/mkosi/blob/main/mkosi/resources/mkosi.md
- https://docs.aws.amazon.com/cli/latest/reference/ec2/create-snapshot.html
- https://docs.aws.amazon.com/cli/latest/reference/ec2/register-image.html
- https://developer.hashicorp.com/packer/integrations/hashicorp/amazon/latest/components/builder/ebssurrogate
