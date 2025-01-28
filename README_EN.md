<div align="center">

<img src="assets/icon.svg" width="160" height="160" style="display: block; margin: 0 auto;" alt="SVG Image">

# **🐶fas-rs-mod🐶**

### Frame aware scheduling for android, work with scene tuner.

[![简体中文][readme-cn-badge]][readme-cn-url]
[![Stars][stars-badge]][stars-url]
[![Download][download-badge]][download-url]
[![Telegram][telegram-badge]][telegram-url]

</div>

> **⚠ Warning**: This document is gpt-translated and may contain inaccuracies or errors.

[readme-cn-badge]: https://img.shields.io/badge/README-简体中文-blue.svg?style=for-the-badge&logo=readme
[readme-cn-url]: README.md
[stars-badge]: https://img.shields.io/github/stars/DdogezD/fas-rs-mod?style=for-the-badge&logo=github
[stars-url]: https://github.com/DdogezD/fas-rs-mod
[download-badge]: https://img.shields.io/github/downloads/DdogezD/fas-rs-mod/total?style=for-the-badge&logo=download
[download-url]: https://github.com/DdogezD/fas-rs-mod/releases/latest
[telegram-badge]: https://img.shields.io/badge/Group-blue?style=for-the-badge&logo=telegram&label=Telegram-Topic
[telegram-url]: https://t.me/fas_rs_official/228

## **Introduction**

> If the scene seen by the naked eye can be directly reflected in the scheduling, that is, if the scheduler is placed from the viewer's perspective to decide performance, can perfect performance control and maximum experience be achieved? `FAS (Frame Aware Scheduling)` is this scheduling concept, which tries to control performance by monitoring frame rendering to minimize overhead while ensuring rendering time.

- ### **What is `fas-rs`?**

  - `fas-rs` is a user-space implementation of `FAS (Frame Aware Scheduling)`, which has the advantage of near-universal compatibility and flexibility on any device compared to the kernel-space `MI FEAS`.

- ### **What is `fas-rs-mod`?**

  - `fas-rs-mod` is an unofficial fork of `fas-rs`, including a slightly tweaked version of `fas-rs` and the "scene-patcher"- a script that attempts to patch `scene tuner`'s config to let it work together with `fas-rs`.
  - After installed `fas-rs-mod`, games in `scene`'s game list will use `fas-rs`, while other apps will use `scene tuner`. `scene` features like "core allocation" and "cpu limiter" will still work, but the cpu limiter will not working in games.
  - Extensions for `fas-rs` are mostly compatible with `fas-rs-mod`. But due to some changes in `fas-rs-mod`, you need to install those extension manually.

## **Extension System**

- To maximize user-space flexibility, `fas-rs` has its own extension system. For development instructions, see the [extension template repository](https://github.com/shadow3aaa/fas-rs-extension-module-template).
- With `fas-rs-mod` installed, please extract those extension and copy the `*.lua` script into `/data/adb/fas-rs/extensions` manually.

## **Customization (Configuration)**

- ### **Configuration Path: `/data/adb/fas-rs/games.toml`**

- ### **Parameter (`config`) Description:**

  - **keep_std**

    - Type: `bool`
    - `true`: Always keep the standard configuration profile when merging configurations, retaining the local configuration's application list, and other aspects are the same as false \*
    - `false`: See [default behavior of configuration merging](#configuration-merging)

  - `*`: Default configuration

- ### **Game List (`game_list`) Description:**

  - **`"package"` = `target_fps`**

    - `package`: String, application package name
    - `target_fps`: An array (e.g., `[30, 60, 120, 144]`) or a single integer, representing the target frame rate the game will render to, `fas-rs` will dynamically match at runtime.

- ### **Modes (`powersave` / `balance` / `performance` / `fast` / `pedestal`) Description:**

  - #### **Mode Switching:**

    - `fas-rs-mod` relies on [`scene`](http://vtools.omarea.com). By patches scene's tuner configuration, `fas-rs` now work with `scene` seamlessly.
    - If you have some understanding of programming on Linux, you can switch to the corresponding mode by writing any of the 5 modes to the `/data/adb/fas-rs/mode` node, and you can also read it to know the current mode of `fas-rs`.

  - #### **Mode Parameter Description:**

    - **margin_fps:**

      - Type: `integer/float`
        - Unit: `fps`
        - Additional allowed frame drop

    - **core_temp_thresh:**

      - Type: `integer` or `"disabled"`
      - `integer`: Core temperature to trigger thermal control by `fas-rs` (unit 0.001℃)
      - `"disabled"`: Disable `fas-rs` built-in thermal control

### **Standard Example of `games.toml` Configuration:**

```toml
[config]
keep_std = true

[game_list]
"example.game" = [30, 45, 60, 90, 120, 144]

[powersave]
margin_fps = 6
core_temp_thresh = 80000

[balance]
margin_fps = 4
core_temp_thresh = 90000

[performance]
margin_fps = 2
core_temp_thresh = 95000

[fast]
margin_fps = 0
core_temp_thresh = 95000

[pedestal]
margin_fps = 1
core_temp_thresh = "disabled"
```

## **Configuration Merging**

- ### `fas-rs` has a built-in configuration merging system to address future configuration feature changes. Its behavior is as follows

  - Delete configurations in the local configuration that do not exist in the standard configuration
  - Insert configurations that are missing in the local configuration but exist in the standard configuration
  - Retain configurations that exist in both the standard and local configurations

- ### Note

  - Implemented using automatic serialization and deserialization, unable to preserve comments and other non-serialization necessary information
  - The automatic merging configuration during installation will not be applied immediately to avoid affecting the current version's operation but will replace the local configuration with the merged new configuration on the next restart.

- ### Manual Merging

  - The module will automatically call once every time it is installed
  - Manual example

    ```bash
    fas-rs merge /path/to/std/profile
    ```

## **Compilation**

```bash
# Ubuntu (NDK is required)
apt install gcc-multilib git-lfs clang python3

# ruff (Python lints & format)
pip install ruff

# Rust (Nightly version is required)
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
rustup default nightly
rustup target add aarch64-linux-android armv7-linux-androideabi x86_64-linux-android i686-linux-android
rustup component add rust-src

# Cargo-ndk
cargo install cargo-ndk

# Clone
git clone https://github.com/shadow3aaa/fas-rs
cd fas-rs

# Compile
python3 ./make.py build --release
# Use the `--nightly` option when building(Some nightly flags will be added to produce smaller artifacts)
python3 ./make.py build --release --nightly
```
