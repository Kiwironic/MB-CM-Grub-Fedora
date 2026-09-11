# MB-CM-Grub-Fedora — Chiang Mai GRUB Theme

A GRUB2 bootloader theme with a Chiang Mai (Tha Phae Gate) backdrop and
Thai-license-plate style icons — each OS entry is rendered as an authentic
Chiang Mai license plate (province name เชียงใหม่, stamp, and Thai flag band).

Forked / inspired by [RAGUL01212321's JDM-theme-Grub](https://github.com/RAGUL01212321/JDM-theme-Grub).

## Fixes over the original theme

* **Fonts now actually load.** The original kept `.pf2` files in a `fonts/`
  subdirectory that GRUB's config generator never scans. Here they live at the
  theme root, so `grub2-mkconfig` picks them up automatically (details below).
* **Countdown bar no longer overlaps other elements** — repositioned to a
  centered bar above the footer (`left=25% top=82% width=50%` instead of
  `left=60% top=90% width=25%`).
* **No duplicated hints.** The original drew an "Arrow keys: navigate |
  Enter: select" text label on top of text already baked into the background
  image. The label is removed; the baked-in legend is used.

## Requirements

* GRUB2 (this theme was developed and tested on **Fedora 44, UEFI**)
* The theme files laid out exactly as in this repo — in particular the
  `*.pf2` font files **must sit at the theme root** (or in a subdirectory
  literally named `f/`), not in `fonts/`.

### Why fonts must be at the theme root

Fedora's `/etc/grub.d/00_header` auto-generates `loadfont` lines only for:

```sh
"$themedir"/*.pf2 "$themedir"/f/*.pf2
```

Fonts in `fonts/` (or anywhere else) are silently ignored, and GRUB falls
back to `unicode.pf2` — which is why the original theme's fonts never
applied. Copying the `.pf2` files to `/boot/grub2/fonts/` also does nothing:
nothing ever `loadfont`s them. Keep them at the theme root as shipped here.

## Installation (Fedora, GRUB2)

1. **Copy the theme into place:**

   ```bash
   git clone https://github.com/Kiwironic/MB-CM-Grub-Fedora.git
   sudo cp -r MB-CM-Grub-Fedora /boot/grub2/themes/
   ```

   The folder must end up at `/boot/grub2/themes/MB-CM-Grub-Fedora/`
   containing `theme.txt`, `background.png`, `icons/`, `select_glow/` and the
   `*.pf2` files at the top level.

2. **Edit `/etc/default/grub`** and set:

   ```bash
   GRUB_THEME="/boot/grub2/themes/MB-CM-Grub-Fedora/theme.txt"
   ```

   and make sure this line stays **commented out** (it disables the graphical
   terminal entirely, so no theme can ever be drawn):

   ```bash
   #GRUB_TERMINAL_OUTPUT="console"
   ```

   Optionally set the menu resolution:

   ```bash
   GRUB_GFXMODE=1920x1200,auto
   ```

3. **Regenerate the GRUB config:**

   ```bash
   sudo grub2-mkconfig -o /boot/grub2/grub.cfg
   ```

   On Fedora UEFI, `/boot/grub2/grub.cfg` is the real config — do **not**
   write to `/boot/efi/EFI/fedora/grub.cfg`, which is only a stub that loads
   the real one.

4. **Verify** that the generated config picked up the theme *and* the fonts:

   ```bash
   sudo grep -nE 'set theme|loadfont' /boot/grub2/grub.cfg
   ```

   You should see `set theme=($root)/grub2/themes/MB-CM-Grub-Fedora/theme.txt`
   and one `loadfont` line per `.pf2` file, e.g.
   `loadfont ($root)/grub2/themes/MB-CM-Grub-Fedora/Rajdhani-24.pf2`.
   If the `loadfont` lines are missing, the `.pf2` files are not at the theme
   root — see the note above.

5. **Reboot.**

## Other distributions

* Theme directory: `/boot/grub/themes/` instead of `/boot/grub2/themes/`.
* Regenerate with `sudo grub-mkconfig -o /boot/grub/grub.cfg`
  (or `sudo update-grub` on Debian/Ubuntu).
* The same font rule applies: `*.pf2` at the theme root.

## Customizing

* **Icons:** PNG images in `icons/`, 505×100. GRUB selects an icon by the
  `--class` of each boot entry (e.g. a Fedora entry uses `icons/fedora.png`,
  Windows uses `icons/windows.png`). `fwsetup.png`, `memtest.png`,
  `reboot.png` and `submenu.png` cover utility entries. Add your own plate as
  `icons/<class>.png`.
* **Background:** replace `background.png` (1920×1200 PNG) or change
  `desktop-image:` in `theme.txt`.
* **Fonts:** regenerate the `.pf2` files from the included
  `Rajdhani-Regular.ttf` if you need other sizes:

  ```bash
  grub2-mkfont -s 24 -o Rajdhani-24.pf2 Rajdhani-Regular.ttf
  ```

  The font name referenced in `theme.txt` (e.g. `"Rajdhani Regular 24"`) is
  matched against the name embedded inside the `.pf2`, not the filename.

## Layout

```
MB-CM-Grub-Fedora/
├── theme.txt            # element positions, fonts, colors
├── background.png       # 1920x1200 backdrop
├── Rajdhani-24.pf2      # menu font (theme root — auto-loaded)
├── Rajdhani-32.pf2      # heading font (theme root — auto-loaded)
├── Rajdhani-Regular.ttf # source font for regenerating .pf2
├── icons/               # license-plate icons per OS --class
└── select_glow/         # 9-slice highlight for the selected entry
```

## Credits & Licensing

* Original theme: [RAGUL01212321 / JDM-theme-Grub](https://github.com/RAGUL01212321/JDM-theme-Grub)
* Font: [Rajdhani](https://fonts.google.com/specimen/Rajdhani) (SIL OFL)
* Preview tooling: [grub2-theme-preview](https://github.com/hartwork/grub2-theme-preview)
* Artwork used under CC-BY is attributed in [LICENSE](LICENSE) — keep the
  attribution if you redistribute.
