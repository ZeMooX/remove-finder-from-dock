# Remove / Hide macOS finder from Dock

This repository intends to give a somewhat comprehensive guide, how the the Finder can be unpinned / removed from the Dock on recent versions of macOS.

The primary reason for doing so, is the lack of up to date and complete documentation on how this can be achieved and Apple making it evermore harder to modify the Dock / Finder behavior without providing any built-in solution.

> I strongly advise you to read this entire document carefully, especially if you have no or little prior experience in tampering with system files. Because that's what it ultimately is and thereby brings it's risks.

![](https://raw.githubusercontent.com/ZeMooX/remove-finder-from-dock/main/md-img/dock.png)

## Why?

You might be using a 3rd party file browser, don't want to waste valuable screen space, or simply don't need it.

Also it can't be asked too much to simply remove an icon from the Dock, is it?

## Caveats

Due to macOS relying quiet heavily on Finder for some core system functionality and having to disable several security mechanisms there are some caveats:

- **No** disk encryption (FileVault)

- **No** System Integrity Protection (SIP)

- **No** Signed System Volume (SSV) *(Monterey and newer only)*

- Updating macOS re-enables Finder

- Desktop Icons aren't displayed (can be substituted with a 3rd party app)

- This guide is (so far) entirely untested on any other setup than mentioned below and is for other systems only meant as a general guideline.

## Tested Platform

Apple MacBook Pro 13 – Late 2020 (13.30 ", M1, 16 GB, 512 GB) - macOS Monterey

(Instructions for later MacOS versions provided as well)

![](https://raw.githubusercontent.com/ZeMooX/remove-finder-from-dock/main/md-img/about.png)

## How

Having read all the previous points *meticulously* and still want to get rid of the Finder icon? Let's get started then.

### 1) Disable Security Mechanisms

1. Disable FileVault in the System Preferences -> Security & Privacy -> FileVault

2. Boot into recovery mode:
   
   - Apple Silicon: Turn off the system completely. Press & hold the power button until "Loading startup options" appears.
   
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

### 2) Mount the System Partition

1. Launch the terminal application of your choice.

2. Create a new directory in your home directory. This new directory will be the mount point for the system partition.
   
   ```bash
   mkdir -m777 ~/systemmount
   ```

3. Figure out which is the system partition
   
   ```bash
   $ diskutil list
   
   /dev/disk0 (internal):
      #:                       TYPE NAME                    SIZE       IDENTIFIER
      0:      GUID_partition_scheme                         500.3 GB   disk0
      1:             Apple_APFS_ISC ⁨⁩                        524.3 MB   disk0s1
      2:                 Apple_APFS ⁨Container disk3⁩         494.4 GB   disk0s2
      3:        Apple_APFS_Recovery ⁨⁩                        5.4 GB     disk0s3
   
   /dev/disk3 (synthesized):
      #:                       TYPE NAME                    SIZE       IDENTIFIER
      0:      APFS Container Scheme Physical Store disk0s2  500.3 GB   disk3
      1:                APFS Volume ⁨Macintosh HD⁩            15.4 GB    disk3s1
      3:                APFS Volume ⁨Preboot⁩                 634.2 MB   disk3s2
      4:                APFS Volume ⁨Recovery⁩                882.2 MB   disk3s3
      5:                APFS Volume ⁨Data⁩                    358.0 GB   disk3s5
      6:                APFS Volume ⁨VM⁩                      1.1 GB     disk3s6
   ```

4. Mount the system partition `Macintosh HD` with the following command at our previously created directory`systemmount`. The identifier for your system partition could be different, replace `disk3s1` with the identifier from your system.
   
   ```bash
   sudo mount -o nobrowse -t apfs /dev/disk3s1 ~/systemmount
   ```

### 3) Modify Plist File

Open the following file in an editor of your choice:

macOS versions **older** than Monterey:
```bash
~/systemmount/Library/Preferences/com.apple.finder.plist
```

Monterey and later:
```bash
~/systemmount/Library/CoreServices/Dock.app/Contents/Resources/DockMenus.plist
```
<sub>Thank you [@journey-ad](https://github.com/journey-ad) for the feedback for Sequoia.</sub>


Look for the `finder-running` section and add the following snippet within the `<array>`:

```xml
<dict>
    <key>command</key>
    <integer>1004</integer>
    <key>name</key>
    <string>REMOVE_FROM_DOCK</string>
</dict>
```

### 4) Bless modified file

The act of *Blessing* is required in order to make the modified file system bootable.

macOS versions **older** than Monterey:

```bash
sudo bless --folder "~/systemmount/System/Library/CoreServices/" --bootefi --create-snapshot
```

Monterey and later:

```bash
sudo bless --mount "/Users/<your_username>/systemmount/System/Library/CoreServices" --setBoot --create-snapshot
```

### 5) Reboot

After a reboot you should now be able to `Right Click Finder -> Options -> Remove from Dock`. Enjoy.

## Sources / Credits

- [python - Can I mount the root (system) filesystem as writable in macOS Monterey? - Stack Overflow](https://stackoverflow.com/questions/73048614/can-i-mount-the-root-system-filesystem-as-writable-in-macos-monterey)

- [mac - MacOS 12.3.1 bless folder as bootable snapshot - Super User](https://superuser.com/questions/1715785/macos-12-3-1-bless-folder-as-bootable-snapshot)

- [Feedback by @journey-ad](https://github.com/ZeMooX/remove-finder-from-dock/issues/1)