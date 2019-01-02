# Grub 2 with the setup_var command

A Grub2 EFI image that has the `setup_var` command.
The setup_var command can modify single bytes within the Setup variable. Some of those are critical for booting, so be careful not to brick your system.

## Background

I wanted to enable Intel 'Speed Shift' on my Dell XPS 15 9550 / 9560, and I read online that you could do this by booting some binary blob, and running a 'setup_var' command.
As I do not trust some random binary from the internet, I wanted to compile it myself. I figured out that it was simply a Grub2 standalone EFI image with the 'setup_var' command added.

The source code for setup_var I found here: <https://github.com/datasone/grub-mod-setup_var> but it didn't compile on my system (Ubuntu 18.04), so I forked a recent grub2, and added the command here instead.

### What is Intel 'Speed Shift' (SST)
Good question. I think it is the marketing term for HWP: Hardware P-States. Here's a description from the Linux documentation:
> This driver decides what P-State to use based on the
> requested policy from the cpufreq core. If the processor is capable of
> selecting its next P-State internally, then the driver will offload this
> responsibility to the processor (aka HWP: Hardware P-States). If not, the
> driver implements algorithms to select the next P-State.
<https://www.kernel.org/doc/Documentation/cpu-freq/intel-pstate.txt>

### How do I enable SST on Dell XPS 15 9550

```
setup_var 0xD8 0x1
```

### How do I enable SST on Dell XPS 15 9560

```
setup_var 0x4BC 0x1
```

## Building a Grub2 standalone EFI image

```
./autogen.sh
./configure --with-platform=efi --prefix=/tmp/grub-with-setup_var
make
make install

cd /tmp/grub-with-setup_var
bin/grub-mkstandalone -O x86_64-efi -o modGRUBShell.efi
```

### Booting the Grub2 EFI image

Copy the modGRUBShell.efi file to a FAT32 formated USB drive as `EFI/BOOT/BOOTX64.EFI`.

Reboot, and boot from your USB drive. If it dosen't work, check that UEFI booting is enabled in your BIOS, and that Secure Boot is disabled.

## Acknowledgments

* Check out the header of `grub-core/commands/efi/setup_var.c`
* <http://forum.notebookreview.com/threads/dell-xps-speed-shift.796891/>
* <https://www.kernel.org/doc/Documentation/cpu-freq/intel-pstate.txt>

