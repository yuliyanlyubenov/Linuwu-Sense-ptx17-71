# Unofficial Linux Kernel Module for Acer Gaming RGB Keyboard Backlight and Turbo Mode (Acer Predator , Nitro) (This repo is PTX17-71 port and backported for linux kernel 6.5. (Forked from the 0x7375646F repo))

Note for Acer Predator Triton 17 X (PTX17-71) only fan control is tested so far. This is very experimental early stage of implementation of the 3 fan controls for cpu, gpu fan1 and gpu fan2. 
    
The code base is still in its early stages, as I’ve just started working on developing this kernel module. It's a bit messy at the moment, but I’m hopeful that, with your help, we can collaborate to improve its structure and make it more organized over time.

Forked from [0x7375646F's Linuwu-Sense](https://github.com/0x7375646F/Linuwu-Sense), with aim to support for Acer Predator Triton 17 X unique features like 3 fan controls and per key RGB keyboard controls. Also my goal is to make this code compatible as much as possible with other acer laptops and in general with the original acer-wmi module so at some point I can even make official pull requests.

## 🚀 Installation
To begin, identify your current kernel version:
```bash
uname -r
```

Install the appropriate Linux headers based on your kernel version. This module has been tested with kernel version (6.5.0). If you want 6.12 or 6.13 clone the original repo from 0x7375646F and possibly apply my changes by using git diff or just unix diff utils.
For Arch Linux:
```bash
sudo pacman -S linux-headers
```
Next, clone the repository and build the module:
```bash
git clone https://github.com/0x7375646F/Linuwu-Sense.git
cd Linuwu-Sense
make install
```
The make command will remove the current acer_wmi module and load the patched version.

To Uninstall:
```bash
make uninstall
```
> **⚠️ Warning!**
> ## Use at your own risk! This driver is independently developed through reverse engineering the official PredatorSense app, without any involvement from Acer. It interacts with low-level WMI methods, which may not be tested across all models.

## 🛠️ Usage
# Example Usage and Configuration

Thermal profiles can be easily switched with a single click! 😎 For battery mode, you can choose between Eco and Balanced, while when plugged into AC, you have the options for Quiet, Balanced, Performance, and Turbo. ⚡💻 Each profile will be different for battery and AC, and the thermal and fan settings will automatically adjust based on your current power source. Customize it to fit your preferences! 🌟

---

For **Predator** laptops, the following path is used: `/sys/module/linuwu_sense/drivers/platform:acer-wmi/acer-wmi/predator_sense`

For **Nitro** laptops, the following path is used: `/sys/module/linuwu_sense/drivers/platform:acer-wmi/acer-wmi/nitro_sense`

predator_sense – This directory includes all the features, excluding the custom boot logo functionality.
four_zoned_kb – If your keyboard is four-zoned, this directory provides support for it. Unfortunately, there is no support for per-key RGB keyboards.
Here is how to interact with the Virtual Filesystems (VFS) mounted in this path:

#### **1. Backlight Timeout ⏰**

This feature turns off the keyboard RGB after 30 seconds of idle mode.

- **0** – Disabled
- **1** – Enabled

To check the current status, use:

`cat /sys/module/linuwu_sense/drivers/platform:acer-wmi/acer-wmi/predator_sense/backlight_timeout`

To change the state, use:

`echo 1 | sudo tee /sys/module/linuwu_sense/drivers/platform:acer-wmi/acer-wmi/predator_sense/backlight_timeout`

---

#### **2. Battery Calibration 🔋**

This function calibrates your battery to provide a more accurate percentage reading. It involves charging the battery to 100%, draining it to 0%, and recharging it back to 100%. **Do not unplug the laptop from AC power during calibration.**

- **1** – Start calibration
- **0** – Stop calibration

To check the current status:

`cat /sys/module/linuwu_sense/drivers/platform:acer-wmi/acer-wmi/predator_sense/battery_calibration`

To change the state:

`echo 1 | sudo tee /sys/module/linuwu_sense/drivers/platform:acer-wmi/acer-wmi/predator_sense/battery_calibration`

---

#### **3. Battery Limiter ⚡**

Limits battery charging to 80%, preserving battery health for laptops primarily used while plugged into AC power.

- **1** – Enabled
- **0** – Disabled

To check the current status:

`cat /sys/module/linuwu_sense/drivers/platform:acer-wmi/acer-wmi/predator_sense/battery_limiter`

To change the state:

`echo 1 | sudo tee /sys/module/linuwu_sense/drivers/platform:acer-wmi/acer-wmi/predator_sense/battery_limiter`

---

#### **4. Boot Animation Sound 🎶**

Enables or disables custom boot animation and sound.

- **1** – Enabled
- **0** – Disabled

To check the current status:

`cat /sys/module/linuwu_sense/drivers/platform:acer-wmi/acer-wmi/predator_sense/boot_animation_sound`

To change the state:

`echo 0 | sudo tee /sys/module/linuwu_sense/drivers/platform:acer-wmi/acer-wmi/predator_sense/boot_animation_sound`

---

#### **5. Standard hwmon Fan Speed Controls (like most linux modules)  🌬️**

Controls the CPU and GPU fan speeds.

- **0** – Auto
- **1** – Minimum fan speed (not recommended)
- **255** – Maximum fan speed
- Other values like **50, 135, 170** can be set according to your preference.
- Also note that the hwmon/pwm3 hwmon/pwm3_enabled files will be available only on predators with 1 cou and 2 gpu fans

Example (set CPU to 140, GPU fan1 to 150, GPU fan2 to 150):

`echo 140 | sudo tee /sys/module/linuwu_sense/drivers/platform:acer-wmi/acer-wmi/hwmon/hwmon4/pwm1`
`echo 150 | sudo tee /sys/module/linuwu_sense/drivers/platform:acer-wmi/acer-wmi/hwmon/hwmon4/pwm2`
`echo 150 | sudo tee /sys/module/linuwu_sense/drivers/platform:acer-wmi/acer-wmi/hwmon/hwmon4/pwm3`

---

#### **5.1. Linuwu-Sense not standard Fan Speed Controls  🌬️**

Controls the CPU and GPU fan speeds. (Somewhat easier for command line use but not standard and Linuwu-Sense only)

- **0** – Auto
- **1** – Minimum fan speed (not recommended)
- **100** – Maximum fan speed
- Other values like **50, 55, 70** can be set according to your preference.
- Also note that the GPU fan 2 setting will be ignored for machines with 1 gpu fan

Example (set CPU to 50, GPU fan1 to 70, GPU fan2 to 70):

`echo 50,70,70 | sudo tee /sys/module/linuwu_sense/drivers/platform:acer-wmi/acer-wmi/predator_sense/fan_speed`

---

#### **6. LCD Override 🖥️**

Reduces LCD latency and minimizes ghosting.

- **1** – Enabled
- **0** – Disabled

To check the current status:

`cat /sys/module/linuwu_sense/drivers/platform:acer-wmi/acer-wmi/predator_sense/lcd_override`

To change the state:

`echo 1 | sudo tee /sys/module/linuwu_sense/drivers/platform:acer-wmi/acer-wmi/predator_sense/lcd_override`

---

#### **7. USB Charging ⚡**

Allows the USB charging port to provide power even when the laptop is off.

- **0** – Disabled
- **10** – Provides power until battery reaches 10%
- **20** – Provides power until battery reaches 20%
- **30** – Provides power until battery reaches 30%

To check the current status:

`cat /sys/module/linuwu_sense/drivers/platform:acer-wmi/acer-wmi/predator_sense/usb_charging`

To change the state:

`echo 20 | sudo tee /sys/module/linuwu_sense/drivers/platform:acer-wmi/acer-wmi/predator_sense/usb_charging`

---
## 💻 Keyboard Configuration 
### **Directory: `four_zoned_kb`**

The `four_zoned_kb` directory contains two Virtual File Systems (VFS) that control the RGB backlight behavior of the four-zone keyboard:

1. **`four_zone_mode`**
2. **`per_zone_mode`**

#### **1. Per-Zone Mode (`per_zone_mode`) 🎨**

This mode allows you to set a specific RGB color for each of the four keyboard zones individually. Each zone is represented by an RGB value in hexadecimal format (e.g., `4287f5` where `42` is Red, `87` is Green, and `f5` is Blue).

- **Parameters:**
    
    - The `per_zone_mode` file accepts four parameters, one for each zone, separated by commas.
    - The `per_zone_mode` also accepts brightness value.
    - Each parameter represents the RGB value for a specific zone in the format `RRGGBB`.
- **Example:**

To set all four zones to the same color (`4287f5`) and brightness to full:

`echo 4287f5,4287f5,4287f5,4287f5,100 | sudo tee /sys/module/linuwu_sense/drivers/platform:acer-wmi/acer-wmi/four_zoned_kb/per_zone_mode`

To set each zone with unique colors:

`echo 4287f5,ff5733,33ff57,ff33a6,100 | sudo tee /sys/module/linuwu_sense/drivers/platform:acer-wmi/acer-wmi/four_zoned_kb/per_zone_mode`

When reading (`cat`) the `per_zone_mode` file, the current color values for each zone are displayed in the format:

`4287f5,4287f5,4287f5,4287f5,100`

This indicates the current RGB color for each of the four zones.

### **Four-Zone Mode (`four_zone_mode`) ✨**

The `four_zone_mode` controls advanced RGB effects for your keyboard, requiring seven parameters:

- **Parameters:**
    
    - **Mode (0-7):** Lighting effect type (e.g., static, breathing, wave).
    - **Speed (0-9):** Speed of the effect (if applicable).
    - **Brightness (0-100):** Intensity of the lighting effect.
    - **Direction (1-2):** Direction of the effect (1 = right to left, 2 = left to right).
    - **Red (0-255), Green (0-255), Blue (0-255):** RGB color values.
- **Modes:**
    
    - **0:** Static Mode – Fixed color, no animation.
    - **1:** Breathing Mode – Color fades in and out.
    - **2:** Neon Mode – Neon glow effect, fixed color (black), direction ignored.
    - **3:** Wave Mode – Wave-like effect, color transitions across the keyboard.
    - **4:** Shifting Mode – Shifting light effect, full control over speed, direction, and color.
    - **5:** Zoom Mode – Zoom effect, direction ignored.
    - **6:** Meteor Mode – Meteor-like effect, direction ignored.
    - **7:** Twinkling Mode – Twinkling light effect, direction ignored.
- **Example Command:**
    
    Set to **Neon Mode** with speed 1, full brightness, and top-to-bottom direction:
    
    `echo 3,1,100,2,0,0,0 | sudo tee /sys/module/linuwu_sense/drivers/platform:acer-wmi/acer-wmi/four_zoned_kb/four_zone_mode`
    
    **Explanation:**
    
    - `3`: Neon Mode
    - `1`: Speed (1)
    - `100`: Full brightness
    - `2`: Direction (top to bottom)
    - `0`: Red (black for Neon)
    - `0`: Green (black for Neon)
    - `0`: Blue (black for Neon)
 
The thermal and fan profiles will be saved and loaded on each reboot, ensuring that the settings remain persistent across restarts.

## 🚧 Roadmap:
- [x] 3 Fans controls (1 cpu fan and 2 gpu fans) for the PTX17-71 (Acer Predator Triton 17 X)
- [...] (Partially) Support for the PTX17-71 RGB keyboard per key color controls. Basic USB keyboard modes support for linux in separate repository [OpenRGB fork](https://github.com/yuliyanlyubenov/OpenRGB-PTX17-71)
- [ ] Turbo mode implementation for PTX17-71, and test it with care.
- [...] (Partially) GUI app for fan controls and controllable FAN curve based on sensors temperature. For now standartized the 3 fan controls to use standard linux kernel API-s so lm-sensors/fancontrol other tools can work out of the box.
- [ ] GUI app for keyboard rgb controls to make it noob friendly.
- [x] Module Persistence After Reboot.
- [ ] More device support currently only ( PHN16-71 ) is fully supported, ( PTX17-71 ) 3 fans supported.
- [ ] Made it as much as possible compatible with the original linux acer-wmi module and create pull requests to integrate all features back to the original linux module.

## License
GNU General Public License v3

### 💖 Donations
Donations are completely optional but show your love for open-source development and motivate me to add more features to this project!
Ethereum (ERC20): 0xbfb3108422593146f9c11a5264b5f648f1dee7d0
