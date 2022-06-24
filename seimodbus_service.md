# [seimodbus0] Troubleshootings for startup services and commands

> `seimodbus0` is configured to have a static address of `192.168.1.87`.

## Services can't start up automatically after rebooting

### Description

A service in `/etc/systemd/system` can't start up normally. E.g., in `seimodbus0`, the service `n1seimodbus0-IOC.service` is not active and has to be manually started each time after rebooting (using `sudo systemctl start n1seimodbus0-IOC.service`).

### Try This

Most likely the problem is due to the confliction of configurations such as those for mounting file systems, or the service itself causing error.

- First, use `systemctl status <service-file-name>` to check the current status. In this case, we use `systemctl status n1seimodbus0-IOC.service` and find that it's loaded, but it's inactive (dead) due to "start condition failed: ConditionFileIsExecutable=/opt/rtapps/linux-arm/epics/modules/modbus/bin/linux-arm/modbusApp was not found".
- Second, check if file systems are mounted correctly by `mount` which will mount according to the configuration we've specified in `/etc/fstab`.
  - If we have a non-empty local copy of the mount point folders, the remote file system will not mount properly, and cause the auto startup of our service to fail. In this case, we need to umount it: `umount <folder-location>`, e.g., `umount /opt/rtcds`.
  - If the `umount` operation's not successful, then the last solution is to comment out the lines in `/etc/fstab` and restart.
  - After restarting, making two empty folders: `sudo mkdir /opt/rtcds` and `sudo mkdir /opt/rtapps`, and change the owner and group both to "controls": `sudo chown controls:controls /opt/rtapps/`, `sudo chown controls:controls /opt/rtcds`.
  - Uncomment the lines in `/etc/fstab`, the sample mounting configuration would be like this (without "automount" setting):

```bash
cdsnas0:/c/optional/rtligo /opt/rtcds nfs rw,hard,intr,bg 0 0

cdsnas0:/c/optional/rtapps /opt/rtapps nfs rw,hard,intr,bg 0 0

cdsnas1:/volume1/warehouse1/ligo /ligo nfs rw,bg,hard,intr,rsize=32768,wsize=32768,tcp,vers=3,timeo=600,actimeo=0
```

- Third, mount cds files to local points: `sudo mount -a` will execute the configuration in `/etc/fstab`.
- Next, restart the service by `sudo systemctl restart <service-file-name>`, e.g., `sudo systemctl restart n1seimodbus0-IOC.service`.
- Lastly, restart: `sudo reboot` or logout and log back in.

## Command not found error

### Description

When we use `n1sitemap.sh` in the terminal, it prompts "command not found".

### Analysis

For a customized command to execute successfully, we need to tell the `bash` shell where to look for the specific command / executable file. In this case, the shell doesn't know where the file `n1sitemap.sh` is because we have not set it up.

### Try This

Add the following in `~/.bashrc` file:

```bash
# ALIGO CDS Environmental Setup
if [ -e /ligo/cdscfg/stdsetup.sh ] ; then
  source /ligo/cds/anu/n1/environ/environ.sh
fi
```

And restart the shell: close it and re-open it, or `exec bash`.
