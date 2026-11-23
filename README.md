<!-- SPDX-FileCopyrightText: no
     SPDX-License-Identifier: CC0-1.0
-->

# Calamares for GXDE

`calamares-gxde` is the GXDE distribution's customized build of
[Calamares](https://calamares.io/) 3.4.2, the distribution-independent Linux
system installer. It keeps the proven Calamares module system and installer
logic, but replaces the stock windowed interface with a full-screen,
deepin-installer-style experience tailored to GXDE live media.

Typical users only see this installer once — when installing GXDE. This
repository is mainly intended for GXDE maintainers and packagers who build
the installer and adjust its configuration and branding for a release.

## Highlights

Compared with upstream Calamares, this fork adds:

- A frameless, full-screen UI with a wallpaper background, top-right close
  button, large centered page titles and custom navigation buttons. It also
  works on live sessions without a running window manager.
- A dot-based page indicator instead of the traditional Calamares sidebar,
  plus a deepin-styled exit-confirmation screen.
- A redesigned welcome page with the distribution logo at the top and a large,
  centered language list.
- A redesigned user-creation page: centered form fields with leading icons,
  floating error messages, compact status rows and an in-page avatar picker
  that reads numbered avatars from `gxde-account-faces`.
- Partition editor dialogs embedded into the main window as dedicated pages,
  with a translucent background and centered buttons; the Back/Next buttons
  are hidden while an editor is open.
- A reworked installation page ("Installing" title with a subtitle), a
  transparent slideshow with fade transitions and timer-driven gradients, and
  a finish page with a prominent centered **Reboot Now** button.
- An ARM64 UEFI fix that uses `grub-install --removable`, avoiding failures on
  boards whose firmware has no writable NVRAM or usable EFI variables.
- A Debian binary package named `calamares-gxde` that conflicts with the
  generic `calamares` package.

Source images and the page indicator are adapted from
`deepin-installer-reborn` (GPL-3.0-or-later); the original copyright notices
are retained in the affected files.

## Compatibility: use customized modules and branding

> **Important:** this fork is **not drop-in compatible** with official
> upstream Calamares modules or branding components. They must be customized
> for GXDE.

- **Modules:** the full-screen shell changes shared UI interfaces
  (`ViewStep`, `ViewManager`, `CalamaresWindow`) and several view modules
  (`welcome`, `users`, `partition`, `finished`). Official or prebuilt modules
  from upstream Calamares may fail to load because of ABI differences, or may
  load without the GXDE behavior (embedded partition editors, avatar picker,
  dot page indicator, hidden navigation buttons, and so on). Always build
  modules from this source tree with the same `WITH_*` CMake options, and port
  any third-party module before using it.
- **Branding:** the deepin-style UI requires GXDE-specific branding settings
  (`windowExpanding: fullscreen`, `sidebar: none`, `navigation: none`), the
  bundled `stylesheet.qss`, `:/deepin/` resources, wallpaper, slideshow and
  translations in `src/branding/default/`. An unmodified official branding
  component will not render this interface and may break the layout. Branding
  must be adapted to this fork rather than copied from upstream.

## Repository layout

| Path | Purpose |
| :--- | :--- |
| `settings.conf` | Top-level module sequence and installer behavior |
| `src/branding/default/` | GXDE full-screen branding: `branding.desc`, stylesheet, wallpaper, icons and slideshow |
| `src/modules/` | Calamares view and job modules (welcome, partition, users, bootloader, etc.) |
| `src/qml/` | QML slideshow components |
| `debian/` | Packaging for the `calamares-gxde` binary package |
| `lang/` | Translations |

## Building

Calamares is a C++17, CMake-based application using Qt 6 and KDE Frameworks 6
in the Debian packaging of this branch. The full dependency list is documented
in [CONTRIBUTING.md](CONTRIBUTING.md); helper scripts live in `ci/`.

```sh
git clone https://github.com/GXDE-OS/calamares-gxde.git
cd calamares-gxde
cmake -S . -B build -G Ninja
ninja -C build
```

Useful CMake options include `-DWITH_QT6=OFF` for a Qt 5 build and
`-DSKIP_MODULES="..."` to leave out individual modules. Run `cmake -P
CMakeLists.txt` to print the version.

## Configuring a distribution

The installer's behavior is controlled by `settings.conf` (which modules run
and in which order), by `src/branding/default/branding.desc` (product strings,
wallpaper, window behavior and slideshow), and by the individual `*.conf` files
in `src/modules/`.

The bundled `default` branding still contains sample product strings (for
example `Generic` and `FancyGL`). Replace them with the correct GXDE product
name and URLs before preparing an official release.

For general Calamares documentation, see the
[upstream user guide](https://calamares.io/docs/users-guide/) and the
[Calamares wiki](https://codeberg.org/Calamares/calamares/wiki).

## Contributing

Bug reports and improvements for the GXDE customization are welcome through
the [GXDE repository](https://github.com/GXDE-OS/calamares-gxde). Please keep
changes to the customized UI and packaging here, and report issues in shared
Calamares core code upstream. See [CONTRIBUTING.md](CONTRIBUTING.md) for
development and dependency details.

## License

Calamares is free software; individual files carry their own SPDX license
identifiers. Upstream code is typically GPL-3.0-or-later, with libraries under
BSD-style licenses. See [LICENSES/](LICENSES/) for the full license texts.
