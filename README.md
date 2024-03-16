# ChromeOS Plymouth Theme

Boot splash was ripped from an official chromeOS recovery image at `/usr/share/chromeos-assets/`

## Installation

1. Download the repository:

   ```sh
   git clone https://github.com/e9x/plymouth-theme-chromeos.git
   ```

2. Move the theme to the plymouth folder:

   ```sh
   sudo mv ./plymouth-theme-chromeos/chromeos /usr/share/plymouth/themes/
   ```

3. Set the default theme and build the initrd:

   ```sh
   sudo plymouth-set-default-theme -R chromeos
   ```

## Grub

You may want to hide the grub menu by adding these lines to `/etc/default/grub`:

```
GRUB_TIMEOUT=0
GRUB_HIDDEN_TIMEOUT=0
```

Then update the menu:

(Debian)

```sh
sudo update-grub
```

(Fedora)

```sh
sudo grub2-mkconfig -o /boot/grub2/grub.cfg
```

## Encryption Password (Fedora)

Entering your encryption password or displaying any messages won't work because this theme uses certain fonts and libraries that aren't included in the initrd by default. To fix this:

1. Install scripting and the noto sans font:

   ```sh
   sudo dnf install plymouth-plugin-script google-noto-sans-fonts
   ```

2. Update the initrd hook to include these files:

   ```sh
   sudo tee -a /usr/libexec/plymouth/plymouth-populate-initrd <<- EOF
   inst \${PLYMOUTH_PLUGIN_PATH}/label.so \$INITRDDIR

   font_config_path="/etc/fonts"
   font_confd_path="/usr/share/fontconfig"
   noto_font_path="/usr/share/fonts/google-noto"

   inst \${font_config_path}/fonts.conf \$INITRDDIR
   inst_dir /usr/lib/fontconfig/cache \$INITRDDIR

   inst \${noto_font_path}/NotoSans-Regular.ttf \$INITRDDIR
   inst \${font_confd_path}/57-google-noto-sans.conf \$INITRDDIR
   inst \${font_config_path}/conf.d/57-google-noto-sans.conf \$INITRDDIR

   inst \${noto_font_path}/NotoSansSymbols2-Regular.ttf \$INITRDDIR
   inst \${font_confd_path}/58-google-noto-sans-symbols2.conf \$INITRDDIR
   inst \${font_config_path}/conf.d/58-google-noto-sans-symbols2.conf \$INITRDDIR
   EOF
   ```

3. Rebuild the initrd:

   ```sh
   sudo plymouth-set-default-theme -R chromeos
   ```
