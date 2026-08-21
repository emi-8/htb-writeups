HTB Academy – Linux Fundamentals: Task Scheduling / systemd Service Investigation

Module: Task Scheduling
Path: Linux Fundamentals
Section: System Management → Task Scheduling
Date Solved: 2026-08-21
Skills Demonstrated: Linux system management, systemd service investigation, user-level service discovery, CLI troubleshooting, analytical problem-solving

⸻

Challenge Overview

This HTB Academy exercise is part of the Linux Fundamentals module under System Management → Task Scheduling.

The task involved identifying the service type of dconf.service.

A direct query using systemctl did not return the expected value, so I investigated the underlying service definition instead.

The challenge emphasized:

* Working with systemctl
* Investigating systemd services
* Distinguishing between system-level and user-level service units
* Locating service definition files
* Reading unit files directly when standard commands do not return the expected information

⸻

Investigation Steps

Step 1 – Search for the Service

I first attempted to find the service through systemctl:

systemctl --type=service | grep dconf

This did not provide the information needed to determine the service type.

⸻

Step 2 – Query the Service Type Directly

I then queried the Type property:

systemctl show dconf.service -p Type

The result was:

Type=

Since the value was empty, I needed another way to inspect the service configuration.

⸻

Step 3 – Locate the Service Definition

I searched the filesystem for files associated with dconf.service:

locate dconf.service

The search returned:

/usr/lib/systemd/user/dconf.service
/usr/share/dbus-1/services/ca.desrt.dconf.service

The location:

/usr/lib/systemd/user/dconf.service

showed that this was a user-level systemd service.

⸻

Step 4 – Inspect the Unit File

I opened the systemd unit file directly:

cat /usr/lib/systemd/user/dconf.service

The relevant section showed:

[Unit]
Description=User preferences database
Documentation=man:dconf-service(1)
[Service]
ExecStart=/usr/libexec/dconf-service
Type=dbus
BusName=ca.desrt.dconf

From this configuration, the service type was identified as:

dbus

⸻

Final Result

The dconf.service unit uses:

Type=dbus

The full challenge answer has not been reproduced beyond the technical concept demonstrated in this write-up.

⸻

Additional Lessons

* systemctl show may not always return the expected property value, especially when investigating user-level services.
* systemd user services can be stored under:

/usr/lib/systemd/user/

* locate is useful when the exact unit-file location is unknown.
* Reading the underlying unit file can reveal important configuration details such as:
    * Type
    * ExecStart
    * BusName
    * service description
* When a standard command produces incomplete output, checking the underlying configuration is often an effective troubleshooting strategy.
* Understanding systemd services is useful not only for Linux administration but also for defensive security, because scheduled and persistent services can be relevant during incident investigation.

⸻

Write-up by @emi-8
