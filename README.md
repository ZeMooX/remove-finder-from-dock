# Documentation on how to remove the macOS finder from the Dock

This repository intends to give a somewhat comprehensive guide, how the the Finder can be unpinned / removed from the Dock on recent versions of macOS and Apple Silicone.

The primary reason for doing so, is the lack of up to date and complete documentation on how this can be achieved and Apple making it evermore harder to modify the Dock / Finder behavior without providing any built-in solution.

It is highly advised to read this entire document carefully, especially if you have no or little prior experience in tampering with system files. Because that's what it ultimately is.

---

## Table of Contents

WHY?

IMPLICATIONS

TESTED PLATFORMS

HOW

SOURCES

CONTRIBUTE

---

## Why?

If you are using a 3rd party file browser, the Finder icon in the Dock might be getting in the way, wasting valuable screen space or simply isn't need.

Also it can't be asked too much to simply remove an icon from the Dock now, is it?

---

## Implications

Due to macOS relying quiet heavily on Finder for some core system functionality there are some caveats:

- No disk encryption (FileVault)

- No System Integrity Protection (SIP)

- Monterey and newer only: No Signed System Volume (SSV)

---

## Tested Platforms

Apple MacBook Pro 13 – Late 2020 (13.30 ", M1, 16 GB, 512 GB)

macOS Monterey

---

## How

Having read all the previous points and still want to get rid of the Finder icon? Let's get started then.

### Disable Security Mechanisms

[python - Can I mount the root (system) filesystem as writable in macOS Monterey? - Stack Overflow](https://stackoverflow.com/questions/73048614/can-i-mount-the-root-system-filesystem-as-writable-in-macos-monterey)

1. Disable FileVault in the System Preferences -> Security & Privacy -> FileVault

2. Boot into recovery mode:
   
   - Apple Silicon: Turn of the system completely. Press & hold the power button until "Loading startup options" appears.
   
   - Intel: Restart your Mac and immediately hold down Command + R until you see the Apple logo.

3. Should you get prompted to login, do so with your administrator account.

4. Launch the Terminal from the menu ribbon with Utilities -> Terminal

5. Execute the following command(s):
   
   ```bash
   csrutil disable
   ```
   
   The following command only on Monterey and newer:
   
   ```bash
   csrutil authenticated-root disable
   ```

6. Reboot back into standard mode

### Mount the System Partition

1. Launch a terminal application of your choice.

2. Create a new directory in your home directory. This new directory will be the mount point for the system partition.
   
   ```bash
   mkdir -m777 ~/systemmount
   ```

3. ?? Figure out which is the system partition

4. Mount the system partition with the following command at our previously created directory.
   
   ```bash
   cd ~
   sudo mount -o nobrowse -t apfs /dev/disk1s5 systemmount
   ```

### Modify Plist File

adasd

### Bless modified file

asdasd




