# Docker files for the UniFi controller

This was built and tested with Debian Bookwork (v12) and the "Unifi Network Server" versions 8.1.127 and 8.4.59.  
It was tested with podman but should work with docker.

### Other container solutions
As pointed out by [/u/brwainer](https://old.reddit.com/user/brwainer) in [this reddit thread](https://old.reddit.com/r/Ubiquiti/comments/1d6xaac/another_dockerization_of_the_unifi_controller/), LinuxServer io maintains a [UniFi Network Docker file](https://hub.docker.com/r/linuxserver/unifi-network-application). It does not include the required MongoDB, which can be added as part of a compose file, but you then have to maintain that file, on the other hand it is maintained and updated regularly while this one will be updated sporadically.

### Network Mode
Given that the software needs access to a mix of UDP and TCP ports ([UniFi Required Ports Reference](https://help.ui.com/hc/en-us/articles/218506997-UniFi-Network-Required-Ports-Reference)), and that the idea here is emulating running it locally but keep it easy to port from one laptop to another, it was easier to set it up with `network_mode: "host"`

### Usage
- download the dot deb file from https://ui.com/download
- move it to this current directory
- adjust the name of the package in the Dockerfile (currently "unifi_sysvinit_all.deb")
- create a symlink to your existing data directroy. If this is the first time you are using this setup, create an empty "data" directory: `mkdir data`
- to keep track of the software version in the same directory as the data you can run: `dpkg-deb -I unifi_sysvinit_all.deb  |grep Version > data/VERSION`
- `podman-compose build`
- `podman-compose up -d && podman-compose logs -f`
- point your browser to `https://localhost:8443`. Since it is using a self-signed certificate, ou will need to click on "advanced..." and "Accept the Risks and Continue" (or "Proceed to localhost (unsafe)" on Chromium)
- you can do local admin only (no "UI Account") by choosing "Advanced Setup" and "skip"
- to shut it down: `podman-compose down`

### Backups
Restoring from backup is easier than from old data from an unknown software version.

Clicking on "Download" in Settings > Backups, will create/update the backup file in data/backup, which will be named after the software version with the ".unf" extension. The file is overridden every time you click on the Download button.

The auto backup will only be triggered if the software is running at the scheduled time, which is unlikely if you bring it up only when you need it. Since the downloaded backup is time stamped, moving it to the `data/backup` directory makes it easy to keep everything in one place and have multiple version of the backup.
