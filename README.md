# mupdf + zathura + tabbed

[Zathura](https://pwmt.org/projects/zathura) document viewer using [mupdf](http://mupdf.com) backend and embedded in [tabbed](http://tools.suckless.org/tabbed). [wmctrl](https://sites.google.com/site/tstyblo//wmctrl) is used to control the zathura window - to open it maximized by default and to bring it into focus when new tab is added from command line.

## How to use

`zathura-tabbed 1.pdf 2.pdf 3.epub ...`

## Compiling

Download sources for mupdf, [girara](https://pwmt.org/projects/girara), zathura, [zathura-pdf-mupdf](https://pwmt.org/projects/zathura-pdf-mupdf), tabbed

### mupdf

`make XCFLAGS=-fPIC release`

### girara

```
make PREFIX=/opt/zathura install
make PREFIX=/opt/zathura install-headers
```

### zathura

I have a patch for zathura-0.3.3 that enables multiple files to be opened inside tabbed using `zathura-embedded 1.pdf 2.pdf ...`. Without the patch, only the first pdf opens in tabbed and the rest are opened in separate windows. Apply the patch: `patch <zathura>/zathura/main.c main.diff`

`env PKG_CONFIG_PATH=/opt/zathura/lib/pkgconfig make PREFIX=/opt/zathura WITH_SQLITE=0 WITH_MAGIC=0 LDFLAGS="-Wl,-rpath='\$\$ORIGIN/../lib'" install`

The rpath setting is so that the `zathura` executable can find `libgirara-gtk3.so` at runtime. If the dollar escapes in the command get messed up somehow, you can fix rpath in the executable by running:

`patchelf --set-rpath '$ORIGIN/../lib' /opt/zathura/bin/zathura`

### zathura-pdf-mupdf

1. edit config.mk: prepend to INC `-I<mupdf>/include`, prepend to LIB `-L<mupdf>/build/release`. (replace `<mupdf>` with path to mupdf folder). Since mupdf libraries were built statically, rpath setting is not needed.
2. You might need to install a few system libraries and dev files like libopenjp2 and libssl (for libcrypto)
3. `env PKG_CONFIG_PATH=/opt/zathura/lib/pkgconfig make`
4. Copy `pdf.so` to `/opt/zathura/lib/zathura/pdf.so`

### tabbed

`make prefix=/opt/zathura install`

### Finishing touches

```
cp zathura-tabbed /opt/zathura/bin/
mkdir ~/.config/zathura
cp zathurarc ~/.config/zathura/
cp zathura.desktop ~/.local/share/applications/
xdg-mime default zathura.desktop application/pdf application/oxps application/epub+zip
sudo apt-get install wmctrl
```

Add `/opt/zathura/bin` to path at login (In `~/.profile` for example)

### Current versions

 - Xubuntu 15.04
 - mupdf-1.7a
 - girara-0.2.4
 - zathura-0.3.3
 - zathura-pdf-mupdf-0.2.8
 - tabbed (git commit 55dc32b27b73c121cab18009bf087e95ef3d9c18)
