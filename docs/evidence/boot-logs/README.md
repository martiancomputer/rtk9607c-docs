# Boot-log evidence

This directory contains curated boot/runtime captures used by the documentation.

## first-6.18.39-boot.txt

Maintainer-provided chronology identifies this as the **first boot attempt** of
the newly produced Linux 6.18 port.

The log itself directly establishes that the image:

- is Linux 6.18.39;
- identifies the machine as Realtek RTL9607C;
- brings up four CPUs;
- reaches `/init`;
- starts the minimal BusyBox rootfs and reaches a shell.

It also preserves failures from that first run, including switch/link interrupt,
FleetConntrack API registration and USB probe failures. The artifact is evidence
of a successful kernel/userspace bring-up, **not** evidence that all peripherals
or network datapaths already worked.

### Sanitization

The committed copy replaces the local build `user@host` identity with
`<redacted-user>@<redacted-host>`.

Automated checks found no MAC/BSSID-form values, email addresses, home-directory
paths or IPv4 addresses in the capture. In particular, there was no ISP-side
DHCP lease present to retain.

Original uploaded SHA-256:

```
d4ebb2e283da4387341e5255345e4894f87717e535eb2ec3a37c5e3aa2dcab6c
```

Committed sanitized SHA-256:

```
82272a921a6cae953f76abb0a45521cbc61c1d51fa15994c1510acff413ecb31
```
