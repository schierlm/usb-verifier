usb-verifier
============

Mini linux that can boot from read-only media (e. g. CD-ROM) and create
hashes (SHA-256) of files on a FAT32 volume (usually on USB) and store
the hash list on the volume. Later, it can be booted again to verify if
the files are still the same. For additional protection (against malware
that tries to edit the checksum file), a HMAC is included at the end of
the file, using a key which is hardcoded in the initramfs of the verifier.

If you are super paranoid, you can regenerate the initramfs with a custom
(random) key. Also note that only the visible files on the disk are protected
by the checksums; changes in the MBR gap or "unused space" on the drive will
not be detected.


Usage
-----

Take the kernel (`vmlinuz`) and the initramfs (`initramfs`) and burn it to
a CD, using your favourite bootloader (ISOLINUX, GRUB2, etc.). No kernel
parameters are needed/supported, just giving the kernel and the initramfs
is enough.

Once booted, it will scan all FAT volumes for a file called
`usb-verifier-xxxxxxxx.txt`, where `xxxxxxxx` is the `SIGTOKEN` in your
`secrets` file. Therefore, before running the tool first, you'll have to
create this file (as an empty file). Once the file is found, it will
checksum all the files of this volume (except the aforementioned file) and
compare it with the stored checksums. If checksums (or the HMAC) differ,
you get an option to view the diff or to save the changes (if they were
legitimate). If everything matches, it will just show you this, and you
get a choice to scan again (probably a different USB key) or to reboot
or shutdown the system.


Building
--------

Compile the Linux kernel (for version number see the filename) using the
included config file and place the resulting (monolithic) bzImage in file
`vmlinuz`. Compile busybox using the included config file and place the
resulting (stripped) binary in file `busybox`.

Run the `generate-secrets` script to regenerate the `secrets` file.

If you prefer, you can also take all (or part of) these files from a
binary snapshot.

Finally, run the `generate-initramfs` script to regenerate the `initramfs`
file. You do not need to be root for that.

Now continue as described in the [Usage](#usage) section.
