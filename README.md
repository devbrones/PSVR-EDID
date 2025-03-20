# PSVR-EDID
PSVR EDID file for 120Hz non-desktop mode

Many thanks to [gusmanb](https://github.com/gusmanb/PSVRFramework) for the original edid file that added 120Hz and 90Hz modes. I modified it further to report the `0x0704` Product id that is required for the drm module to set the non-desktop quirk.

### Installation instructions
requires sudo\
create the edid firmware folder:
```
sudo mkdir -p /usr/lib/firmware/edid/
```
move the binary to said folder:
```
sudo mv <Path-to-binary> /usr/lib/firmware/edid/siehmd.bin
```
(For Arch linux) Include the file in the initcpio:\
in `/etc/mkinitcpio.conf`
```
FILES=(/usr/lib/firmware/edid/siehmd.bin)
```
run `sudo mkinitcpio -P`

Find the port that the HMD is connected to:
(For sway) run `swaymsg -pt get_outputs`\
(For X11) run `xrandr`\

Run `drm_info | less` and find the appropriate connector.

Update your cmdline:\
(For GRUB) edit `/etc/default/grub`:\
```
GRUB_CMDLINE_LINUX="<...> drm.edid_firmware=DP-1:edid/siehmd.bin"
```

Reboot:\
`reboot`

Check if the system sees the HMD as non-desktop using the same command that was used to find the connector.

`drm_info` should show `│   │       ├───"non-desktop" (immutable): range [0, 1] = 1`

`swaymsg -pt get_outputs` should show
```
Output DP-1 'Sony 0x0704 Unknown' (non-desktop)
  Available modes:
    1920x1080 @ 60.000 Hz
    1920x1080 @ 120.000 Hz
    1920x1080 @ 120.000 Hz
    1920x1080 @ 90.000 Hz
    1920x1080 @ 90.000 Hz
    640x480 @ 59.952 Hz
    640x480 @ 59.940 Hz
```

Now you can configure your compositor to use the 120Hz mode.\
(For sway) add `output <Connector> mode 1920x1080@120Hz`


[PSVRFramework edid file](https://github.com/gusmanb/PSVRFramework/wiki/Video-routing-and-EDIDs)
