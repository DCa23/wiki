# common start (libremesh qmp/v1)

download required packages: https://github.com/libremesh/lime-sdk#building-in-running-system

you require some space. ubnt-nano-m-xw takes ~900 MB

go inside repo

    git clone https://github.com/libremesh/lime-sdk
    cd lime-sdk

download feeds

    ./cooker -f

apply patches

    snippets/regdbtz.sh
    snippets/regdbus.sh

## option1: if you don't have kernel patches to apply

build archictecture

    ./cooker -b=ar71xx/generic

build specific device, for example:

    ./cooker -c ar71xx/generic --profile=ubnt-nano-m-xw --flavor=lime_default --remote --community=qmp/v1

## option2: if you have kernel patches to apply

apply patch, for example:

    snippets/regdbtz.sh

build architecture and force all compilations locally or it will not apply. it gives interesting outputs: image builder and sdk in the `tmp` directory

    ./cooker -b ar71xx/generic --force-local

build specific device, for example:

    ./cooker -c ar71xx/generic --profile=ubnt-nano-m-xw --flavor=lime_default --community=qmp/v1

## extra: qMp compatibility - known problems

- DHCP can be down for 5 min. Probably the workaround is to use odhcp instead of dnsmasq https://github.com/libremesh/network-profiles/blob/master/kollserola/generic/etc/uci-defaults/zz-use-odhcpd
    - https://github.com/libremesh/lime-web/issues/55
- essential is has name advanced in menu tab? http://10.202.45.1/cgi-bin/luci/lime/essentials
- Wifi settings in http://10.202.45.1/cgi-bin/luci/lime/essentials.
    - Select wifi channel requires typing. We need a combobox
    - No field to change 20 Mhz or 40 Mhz. Combobox?

# in a virtual machine

    ./cooker -c x86/64 --profile=Generic --flavor=normal_node

- proxmox (qcow2) [./openwrt-proxmox.md]()
- virtualbox (vdi): `VBoxManage convertdd lede-17.01.4-normal-node-x86-64-combined-ext4.img lede-17.01.4-normal-node-x86-64-combined-ext4.vdi` src https://forums.virtualbox.org/viewtopic.php?t=8919

# specific linux kernel options

inside lime-sdk repo, navigate to `feeds/base`, there you can manage the classic `make menuconfig`

# Q&A

- Q: I don't know what are the available architectures
    - A: target list (choose device's architecture)
        ./cooker --targets

- Q: ... and the available profiles?
    - A: you need to know the target, from there you use profiles argument, for example:
        ./cooker --profiles=ar71xx/generic

- Q: My wifi is not working, how can I debug wifi?
    - A: `iwinfo`, `iw list`, `iw dev`
        - extra: `iwinfo wlan0 assoc`, `iwinfo wlan0 info`, `iwinfo wlan0 txpowerlist`, `iwinfo wlan0 freqlist`, `iw phy phy0 info`

- Q: Show me the link to the network profile that configures qMp named qmp/v1
    - A: https://github.com/libremesh/network-profiles/tree/master/qmp/v1/etc/config

- Q: How to included config files in the firmware image?
    - A: place them in files directory, from there place the path, for example: files/etc/config/system.conf

# extra references

the new steps in qmp at the bottom: http://qmp.cat/News/35_qMp_4.0_Macondo_released
