## What is System Integrity Protection and why does it need to be disabled?

System Integrity Protection ("rootless") is a security feature of macOS first introduced in 10.13, then further locked down in 10.14. It protects some files and directories from being tampered with—even from the root user. 

During development, it may be necessary to disable SIP temporarily to install and test your code. There is no need to disable SIP to run and debug apps from Xcode but might need to disable it to install system extensions such as DriverKit drivers.

>[!WARNING]
>Disable SIP only temporarily to perform necessary tasks, and reenable it as soon as possible.
>Failure to reenable SIP when you are done testing leaves your computer vulnerable to malicious code.

## How to disable System Integrity Protection?

1. Turn off your device

2. Intel [(Apple docs)](https://support.apple.com/en-gb/guide/mac-help/mchl338cf9a8/12.0/mac/12.0):
   
   Hold down <kbd>command ⌘</kbd><kbd>R</kbd> while booting your device.

   Apple Silicon [(Apple docs)](https://support.apple.com/en-gb/guide/mac-help/mchl82829c17/12.0/mac/12.0):
   
   Press and hold the power button on your Mac until “Loading startup options” appears. Click Options, then click Continue.

3. In the menu bar, choose `Utilities`, then `Terminal`

4. 
```zsh
#
# APPLE SILICON
#

# If you're on Apple Silicon macOS 13.x.x OR newer
# Requires Filesystem Protections, Debugging Restrictions and NVRAM Protection to be disabled
# (printed warning can be safely ignored)
csrutil enable --without fs --without debug --without nvram

# If you're on Apple Silicon macOS 12.x.x
# Requires Filesystem Protections, Debugging Restrictions and NVRAM Protection to be disabled
# (printed warning can be safely ignored)
csrutil disable --with kext --with dtrace --with basesystem

#
# INTEL
#

# If you're on Intel macOS 11.x.x OR newer
# Requires Filesystem Protections and Debugging Restrictions to be disabled (workaround because --without debug does not work)
# (printed warning can be safely ignored)
csrutil disable --with kext --with dtrace --with nvram --with basesystem
```
5. Reboot

6. For Apple Silicon—enable non-Apple-signed arm64e binaries
```zsh
# Open a terminal and run the below command, then reboot
sudo nvram boot-args=-arm64e_preview_abi
```

7. Verify that SIP is turned off by running `csrutil status`, which returns `System Integrity Protection status: disabled`. if it is turned off (it may show `unknown` for newer versions of macOS when disabling SIP partially)

If you ever want to re–enable SIP; repeat the steps above, but run `csrutil enable` instead at step 4.

>[!NOTE]
>SIP will be re–enabled during device repairs or analysis at any Apple Retail Store or Apple Authorised Service Provider.
>You will have to repeat this step after getting your device back.
