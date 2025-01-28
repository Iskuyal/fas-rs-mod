<div align="center">

<img src="assets/icon.svg" width="160" height="160" style="display: block; margin: 0 auto;" alt="SVG Image">

# **🐶fas-rs-mod🐶**

### Frame aware scheduling for android, work with scene tuner.

[![English][readme-en-badge]][readme-en-url]
[![Stars][stars-badge]][stars-url]
[![Download][download-badge]][download-url]
[![Telegram][telegram-badge]][telegram-url]

</div>

[readme-en-badge]: https://img.shields.io/badge/README-English-blue.svg?style=for-the-badge&logo=readme
[readme-en-url]: README_EN.md
[stars-badge]: https://img.shields.io/github/stars/DdogezD/fas-rs-mod?style=for-the-badge&logo=github
[stars-url]: https://github.com/DdogezD/fas-rs-mod
[download-badge]: https://img.shields.io/github/downloads/DdogezD/fas-rs-mod/total?style=for-the-badge
[download-url]: https://github.com/DdogezD/fas-rs-mod/releases/latest
[telegram-badge]: https://img.shields.io/badge/Group-blue?style=for-the-badge&logo=telegram&label=Telegram-Topic
[telegram-url]: https://t.me/fas_rs_official/228

## **简介**

> 假如肉眼看到的画面能直接反映在调度上，也就是说以把调度器放在观看者的角度来决定性能，是否就能实现完美的性能控制和最大化体验? `FAS (Frame Aware Scheduling)`就是这种调度概念，通过监视画面渲染来尽量控制性能以在保证渲染时间的同时实现最小化开销

- ### **什么是`fas-rs`?**

  - `fas-rs`是运行在用户态的`FAS(Frame Aware Scheduling)`实现，对比核心思路一致但是在内核态的`MI FEAS`有着近乎在任何设备通用的兼容性和灵活性方面的优势
 
- ### **什么是`fas-rs-mod`?**

  - `fas-rs-mod`是通过修补`scene`配置文件，使`fas-rs`与`scene`一同工作的`fas-rs`修改版

## **插件系统**

- 为了最大化用户态的灵活性，`fas-rs`有自己的一套插件系统，开发说明详见[插件的模板仓库](https://github.com/shadow3aaa/fas-rs-extension-module-template)
- `fas-rs-mod`的插件兼容性与官方版本基本相同，但仍有一些插件不兼容，如`fas-ext`。

## **自定义(配置)**

- ### **配置路径: `/sdcard/Android/fas-rs/games.toml`**

- ### **参数(`config`)说明:**

  - **keep_std**

    - 类型: `bool`
    - `true`: 永远在配置合并时保持标准配置的 profile，保留本地配置的应用列表，其它地方和 false 相同 \*
    - `false`: 见[配置合并的默认行为](#配置合并)

  - `*`: 默认配置

- ### **游戏列表(`game_list`)说明:**

  - **`"package"` = `target_fps`**

    - `package`: 字符串，应用包名
    - `target_fps`: 一个数组(如`[30，60，120，144]`)或者单个整数，表示游戏会渲染到的目标帧率，`fas-rs`会在运行时动态匹配

- ### **模式(`powersave` / `balance` / `performance` / `fast`/ `pedestal`)说明:**

  - #### **模式切换:**

    - `fas-rs-mod`依赖于[`scene`](http://vtools.omarea.com)的配置接口,通过修补scene配置文件，实现`fas-rs`与`scene`一同工作
    - 如果你有在 linux 上编程的一些了解，向`/dev/fas_rs/mode`节点写入 5 模式中的任意一个即可切换到对应模式，同时读取它也可以知道现在`fas-rs`所处的模式

  - #### **模式参数说明:**

    - **margin_fps:**

      - 类型: `整数/浮点数`
      - 单位: `fps`
      - 额外允许的掉帧量

    - **core_temp_thresh:**

      - 类型: `整数`或者`"disabled"`
      - `整数`: 让`fas-rs`触发温控的核心温度(单位0.001℃)
      - `"disabled"`: 关闭`fas-rs`内置温控

### **`games.toml`配置标准例:**

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

## **配置合并**

- ### `fas-rs`内置配置合并系统，来解决未来的配置功能变动问题。它的行为如下

  - 删除本地配置中，标准配置不存在的配置
  - 插入本地配置缺少，标准配置存在的配置
  - 保留标准配置和本地配置都存在的配置

- ### 注意

  - 使用自动序列化和反序列化实现，无法保存注释等非序列化必须信息
  - 安装时的自动合并配置不会马上应用，不然可能会影响现版本运行，而是会在下一次重启时用合并后的新配置替换掉本地的

- ### 手动合并

  - 模块每次安装都会自动调用一次
  - 手动例

    ```bash
    fas-rs merge /path/to/std/profile
    ```

## **编译**

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

## **捐赠**

[🐷🐷的爱发电](https://afdian.com/a/shadow3qaq)，你的捐赠可以增加🐷🐷维护开发此项目的动力。

🐶🐶为爱发电，不接受任何形式的捐赠。但你给🐷的捐赠可以让🐶吃到更香的烤🐷！
