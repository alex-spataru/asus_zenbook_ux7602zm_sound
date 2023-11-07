<p align="center">
  <img src="tux.svg" width="120" alt="Tux" />
</p>

<p align="center">SSDT Patch to fix speakers sound on Asus Zenbook UX7602ZM<br/></p>

# Pre Requirements

### Kernel patch

As of now, the Linux kernel does not contain the `ALC245_FIXUP_CS35L41_SPI_2` patch for this laptop model. In order to make the SSDT patch work, you need to modify the `kernel_source_root/sound/pci/hda/patch_realtek.c` file as follows:

1. Pull the kernel source code.

2. Open `kernel_source_root/sound/pci/hda/patch_realtek.c` with your text editor and search for `ALC245_FIXUP_CS35L41_SPI_2` until you find a list of definitions like this:

```c
SND_PCI_QUIRK(0x1043, 0x3a50, "ASUS G834JYR/JZR", ALC245_FIXUP_CS35L41_SPI_2),
```

3. Insert the configuration for the UX7602ZM laptop:

```c
SND_PCI_QUIRK(0x1043, 0x1f62, "ASUS UX7602ZM", ALC245_FIXUP_CS35L41_SPI_2),
```

4. Compile & install the kernel:

```bash
make -j $(nproc)
sudo make modules_install
sudo make install
```

**Note:** I have [submited](https://lore.kernel.org/all/87wmutr6z3.wl-tiwai@suse.de/) a kernel patch with these changes, it might be available for version 6.7.

# Patch installation

### 1. Build the *ssdt-csc3551.dsl* file

```
sudo apt install acpica-tools
iasl -tc ssdt-csc3551.dsl
```

### 2. Copy it in /boot

```
sudo cp -f ssdt-csc3551.aml /boot
```

### 3. Copy the GRUB script

```
sudo cp -f 01_acpi /etc/grub.d && sudo chmod +x /etc/grub.d/01_acpi
```

### 4. Update the GRUB configuration

```
sudo update-grub
```

### 5. Reboot and select the new kernel in the GRUB menu

### 6. If everything works, remove the old kernel & enjoy!

#### Special thanks

| User                                         |                                                                                      Description                                                                                       |
| :------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: |
| [lamperez](https://gist.github.com/lamperez) | For [ACPI DSDT/SSDT patching guides](https://gist.github.com/lamperez/862763881c0e1c812392b5574727f6ff) and [tools](https://gist.github.com/lamperez/d5b385bc0c0c04928211e297a69f32d7) |
| [Moooebie](https://gist.github.com/Moooebie) |                For [ACPI DSDT/SSDT patching guide](https://gist.github.com/lamperez/862763881c0e1c812392b5574727f6ff?permalink_comment_id=4582983#gistcomment-4582983)                 |
| [thor2002ro](https://github.com/thor2002ro)  |                                        For [GRUB patch](https://github.com/thor2002ro/asus_zenbook_ux3402za/tree/main/Sound) for similar laptop                                        |
| [badgers-ua](https://github.com/badgers-ua)  |                                        For [kernel patch instructions](https://github.com/badgers-ua/asus_zenbook_ux5304va_sound) for similar laptop                                        |

