# Task 2: Preparation

Before creating a forensic image, the target drive must be identified and prepared. Proper logging and evidence preservation are essential throughout the process.

## Write-Blockers

A **write-blocker** prevents modifications to a storage device during analysis, ensuring that the original evidence remains unchanged.

## Audit Trail

An audit trail records every action performed during the investigation. Bash history and session logging tools can be used to preserve command history.

### History Commands

Enable command history:

```bash id="i0g8kj"
set -o history
```

Append commands to the history file instead of overwriting:

```bash id="e8jrmn"
shopt -s histappend
```

Record all commands:

```bash id="w4jjmt"
export HISTCONTROL=
export HISTIGNORE=
```

Specify the history file:

```bash id="s7k9tb"
export HISTFILE=~/.bash_history
```

Remove limits on history size:

```bash id="jlwm4r"
export HISTFILESIZE=-1
export HISTSIZE=-1
```

Add timestamps to commands:

```bash id="3jwk1m"
export HISTTIMEFORMAT="%F-%R "
```

Display command history:

```bash id="6uf8pj"
history
```

Session logging tools such as `script` and `ttyrec` can also be used to maintain an audit trail.

## Device Identification

### Display Mounted File Systems

```bash id="prh71v"
df
```

The `df` command shows mounted file systems and disk usage information.

### List All Block Devices

```bash id="n8t1rq"
lsblk -a
```

Lists all block devices, including unmounted disks.

### View Loop Device Information

```bash id="j4bmqx"
sudo losetup -l /dev/loop11
```

Displays the backing file associated with the loop device.

### View Filesystem Information

```bash id="k0tt9r"
sudo blkid /dev/loop11
```

Displays the filesystem type and UUID.

## Questions

### What command can be used to list all block devices in Linux OS?

**Answer**

```text id="8ud4ij"
lsblk
```

### Which bash command displays all commands executed in a session?

**Answer**

```text id="uz5vka"
history
```

