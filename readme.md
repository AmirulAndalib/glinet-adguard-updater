# AdGuard Home Update Script for GL.iNet Routers

<img src="images/screen.jpg" width="400" align="right" alt="Profile Picture" style="border-radius: 10%;">

This script is designed to update AdGuard Home on GL.iNet routers.

It was created by [Admon](https://forum.gl-inet.com/u/admon/) for the GL.iNet community and tested on the MT-6000 (Flint 2) and GL-BE9300 (Flint 3) with firmware 4.7 and 4.8 but works fine on nearly all GL.iNet routers.

[![Prebuild AdGuard Home for GL.iNet devices](https://github.com/Admonstrator/glinet-adguard-updater/actions/workflows/build-adguardhome.yaml/badge.svg)](https://github.com/Admonstrator/glinet-adguard-updater/actions/workflows/build-adguardhome.yaml)

## Usage

Run the script with the following command:

```shell
./update-adguardhome.sh [--ignore-free-space] [--select-release]
```

You can run it without cloning the repository by using the following command:

```shell
wget -O update-adguardhome.sh https://raw.githubusercontent.com/Admonstrator/glinet-adguard-updater/main/update-adguardhome.sh && sh update-adguardhome.sh
```

## Tiny-AdGuardHome

By default, this script will use a pre-compressed version of AdGuard Home. This version is optimized for GL.iNet routers and saves plenty of space. Instead of 32 MB, the compressed version is only 6 MB.

## Running on devices with low free space

You can use `--ignore-free-space` to ignore the free space check. This is useful for devices with low free space.

In that case there will be no backup of the original files and the script will not check if there is enough free space to download the new files. Could potentially break your router if there is not enough free space. It's not recommended to use this option!

## Enabling query logging

By default, AdGuard Home is not able to save the query log to a file. This is to prevent running out of space on the router and prevent wearing out the flash memory. This script can enable query logging to file by setting the `querylog` option in the AdGuard Home configuration file. You will asked if you want to enable query logging after the update.

If you want to enable query logging to file without updating AdGuard Home, you can run the following command:

```shell
sed -i '/^querylog:/,/^[^ ]/ s/^  file_enabled: .*/  file_enabled: true/' /etc/AdGuardHome/config.yaml
/etc/init.d/adguardhome restart
```

For disabling query logging to file, you can run the following command:

```shell
sed -i '/^querylog:/,/^[^ ]/ s/^  file_enabled: .*/  file_enabled: false/' /etc/AdGuardHome/config.yaml
/etc/init.d/adguardhome restart
```

## Selecting a release

By default, the script will install the latest stable release of AdGuard Home. You can use `--select-release` to select a specific release. The script will ask you which release you want to install.

## Feedback

Feel free to provide feedback in the [GL.iNet forum](https://forum.gl-inet.com/t/script-update-adguard-home/39398).

## Reverting

Since AdGuard Home is deeply integrated into the firmware, it is difficult to revert the changes. Right now there is no automatic way to revert them. Resetting the router to factory settings will **NOT** revert the changes if you choose to make the installation persistent!

How to revert the changes manually by SSH:

1. Remove the line `/usr/bin/enable-adguardhome-update-check` from `/etc/rc.local`.
2. Execute `rm /usr/bin/enable-adguardhome-update-check` to remove the script.
3. Remove the following lines from `/etc/sysupgrade.conf`:
    - `/root/AdGuardHome_backup.tar.gz`
    - `/etc/AdGuardHome`
    - `/usr/bin/AdGuardHome`
    - `/usr/bin/enable-adguardhome-update-check`
    - `/etc/rc.local`

4. Stop the AdGuard Home service by executing `/etc/init.d/adguardhome stop`.
5. Reset the AdGuard Home configuration by executing `rm -rf /etc/AdGuardHome` - this will remove all your settings and blocklists!
6. Start the AdGuard Home service by executing `/etc/init.d/adguardhome start`.

A backup of the original files is located in the `/root/` folder. The backup is named `AdGuardHome_backup.tar.gz`.

If you still encounter issues after doing these steps, you can reset AdGuard Home to its original state by flashing the firmware again.

## Disclaimer

This script is provided as is and without any warranty. Use it at your own risk.

**It's a really early stage and definitely not ready for production use.**

**It may break your router, your computer, your network or anything else. It may even burn down your house.**

**You have been warned!**