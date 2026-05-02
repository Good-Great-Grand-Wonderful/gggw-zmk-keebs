# gggw-zmk-keebs

The shared ZMK module powering [Good Great Grand Wonderful](https://ergokeyboards.com) keyboards — currently **Levels** and **Crosses**.

This repo is consumed as a [Zephyr / ZMK module](https://zmk.dev/docs/development/new-shield#zmk-config). Per-keyboard user-config repos pull it in via their `config/west.yml` so that board definitions, shields, drivers, and default keymaps stay in one place instead of being copy-pasted across builds.

## What it provides

### Boards (`boards/gggw/`)

- **levels54** — 54-key hotswappable split with dual trackballs and VIK extensibility. Built as `levels54_left` / `levels54_right`.

### Shields (`boards/shields/`)

- **crosses** — split shield for the `nice_nano_v2` with OLED and pointer support. Built as `crosses_left` / `crosses_right`.

### Upstream modules pinned in `config/west.yml`

- [`efogdev/zmk-pmw3610-driver`](https://github.com/efogdev/zmk-pmw3610-driver) — PMW3610 trackball sensor driver
- [`efogdev/zmk-report-rate-limit`](https://github.com/efogdev/zmk-report-rate-limit) — HID report rate limiting
- [`badjeff/zmk-input-processor-xyz`](https://github.com/badjeff/zmk-input-processor-xyz) — XYZ input processor
- [`caksoylar/zmk-rgbled-widget`](https://github.com/caksoylar/zmk-rgbled-widget) — RGB LED status widget

ZMK itself is pinned in the same manifest — bumping it here is the single source of truth for every keyboard that depends on this module.

## Using it from a keyboard config repo

Add this module as a project in your user-config's `config/west.yml`:

```yaml
manifest:
  remotes:
    - name: gggw
      url-base: https://github.com/Good-Great-Grand-Wonderful
  projects:
    - name: gggw-zmk-keebs
      remote: gggw
      revision: main
      import: config/west.yml
  self:
    path: config
```

Then declare your build targets in `build.yaml` (see [this repo's `build.yaml`](build.yaml) for working examples covering `levels54_*`, `crosses_*`, and the `settings_reset` shield).

## Building this module standalone

The included [`build.yaml`](build.yaml) lets CI build every target directly from this repo, which is handy for smoke-testing changes without a downstream config repo. Push to a branch and grab `firmware.zip` from the GitHub Actions run.

For a local build, initialize a ZMK workspace pointing at `config/west.yml` and run e.g.:

```sh
west build -s zmk/app -d build/levels54_left -b levels54_left
west build -s zmk/app -d build/crosses_left -b nice_nano -- -DSHIELD=crosses_left
```

Flash by double-tapping reset and copying `zephyr/zmk.uf2` to the mounted bootloader drive. The `settings_reset` shield clears bonded BLE pairings — flash it, then re-flash regular firmware.

## License

[MIT](LICENSE) © Good Great Grand Wonderful
