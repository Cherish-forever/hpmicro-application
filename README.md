# HPmirco Zephyr Example Application

This repository contains a Zephyr example application. The main purpose of this
repository is to serve as a reference on how to structure Zephyr-based
applications. Some of the features demonstrated in this example are:

- Basic [Zephyr application][app_dev] skeleton
- [Zephyr workspace applications][workspace_app]
- [Zephyr modules][modules]
- [West T2 topology][west_t2]
- [Custom boards][board_porting]
- Custom [devicetree bindings][bindings]
- Out-of-tree [drivers][drivers]
- Out-of-tree libraries
- Example CI configuration (using Github Actions)
- Custom [west extension][west_ext]

This repository is versioned together with the [Zephyr main tree][zephyr]. This
means that every time that Zephyr is tagged, this repository is tagged as well
with the same version number, and the [manifest](west.yml) entry for `zephyr`
will point to the corresponding Zephyr tag. For example, the `example-application`
v2.6.0 will point to Zephyr v2.6.0. Note that the `main` branch always
points to the development branch of Zephyr, also `main`.

[app_dev]: https://docs.zephyrproject.org/latest/develop/application/index.html
[workspace_app]: https://docs.zephyrproject.org/latest/develop/application/index.html#zephyr-workspace-app
[modules]: https://docs.zephyrproject.org/latest/develop/modules.html
[west_t2]: https://docs.zephyrproject.org/latest/develop/west/workspaces.html#west-t2
[board_porting]: https://docs.zephyrproject.org/latest/guides/porting/board_porting.html
[bindings]: https://docs.zephyrproject.org/latest/guides/dts/bindings.html
[drivers]: https://docs.zephyrproject.org/latest/reference/drivers/index.html
[zephyr]: https://github.com/zephyrproject-rtos/zephyr
[west_ext]: https://docs.zephyrproject.org/latest/develop/west/extensions.html

## Getting Started

Before getting started, make sure you have a proper Zephyr development
environment. Follow the official
[Zephyr Getting Started Guide](https://docs.zephyrproject.org/latest/getting_started/index.html).

### Initialization

The first step is to initialize the workspace folder (``my-workspace``) where
the ``example-application`` and all Zephyr modules will be cloned. Run the following
command:

```shell
# initialize my-workspace for the example-application (main branch)
west init -m https://github.com/Cherish-forever/hpmicro-application --mr hpmicro my-workspace
# update Zephyr modules
cd my-workspace
west update
```
###  Change the default build directory
```shell
west config build.dir-fmt "build/{app}/{board}"
```

### Building and running

To build the application, run the following command:

```shell
west build -b hpm6750evkmini -s zephyr/samples/basic/blinky
```

### Install HPmirco openocd
```shell
git clone -b riscv-hpmicro https://github.com/hpmicro/riscv-openocd.git && cd riscv-openocd
sudo apt install make libtool pkg-config autoconf automake texinfo
./bootstrap
./configure
make -j4
sudo make install
```

If you are using zephyr SDK to debug firmware, you need replace openocd in zephyr SDK by: (NOT RECOMMENDED!)
```shell
./configure --prefix <path to zephyr sdk>/sysroots/x86_64-pokysdk-linux/usr
make -j4
sudo make install
```


### Flash
```shell
west flash -d build/blinky/hpm6750evkmini/
```
I don`t know why flash the binary not works.

### Debug
```shell
west debug -d build/blinky/hpm6750evkmini/
```
set break at main
```shell
b main.c:main
```

continue run
```shell
c
```

where `$BOARD` is the target board.

You can use the `custom_plank` board found in this
repository. Note that Zephyr sample boards may be used if an
appropriate overlay is provided (see `app/boards`).

A sample debug configuration is also provided. To apply it, run the following
command:

```shell
west build -b $BOARD app -- -DOVERLAY_CONFIG=debug.conf
```

You can also use it together with the `rtt.conf` file if using Segger RTT. Once
you have built the application, run the following command to flash it:

```shell
west flash
```
