# 无弦之琴

基于 Tang Nano 20K FPGA 与 ESP8266 的八键电子琴项目，支持本地触摸演奏、八声部和弦、SD 曲目播放、跟弹提示及手机网页控制。本仓库由提供的 `app.7z` 整理，FPGA 为 1.4 暖橙 LCD 输出稳定版，ESP8266 为 1.4 暖橙 MAB 版。

## 功能

- 八个触摸键和八声部合成，三角波、正弦波、脉冲波三种音色。
- M0 全部琴键使用 A；M1 上排四键使用 A、下排四键使用 B；M2 每个音符混合 A+B。
- 480×272 RGB LCD、音量控制、FSR 压力弯音、颤音、短回声及节拍提示。
- FAT16/FAT32 SD 播放、NTE/NTE2 跟弹，以及 ESP8266 管理的 FAT32 文件上传。
- ESP8266 提供本地 Wi-Fi 热点与网页界面，通过 UART 和 FPGA 通信。

## 本仓库包含什么

| 目录 | 内容 |
| --- | --- |
| [`WuxianZhiQin_FPGA_v1.4_Orange_LCDfix`](WuxianZhiQin_FPGA_v1.4_Orange_LCDfix) | 13 个 RTL 模块、高云工程、管脚与时钟约束、封面与格式转换工具 |
| [`WuxianZhiQin_ESP8266_v1.4_Orange_MAB`](WuxianZhiQin_ESP8266_v1.4_Orange_MAB) | Arduino 草图、网页源文件、已生成的 `phone_assets.h` 与资源生成脚本 |
| [`firmware`](firmware) | 压缩包内已有的 FPGA `.fs`、`.bin`、`.binx`，原样保留 |
| [`docs`](docs) | 架构、构建、硬件接口及目录说明 |

**压缩包未包含 Android 工程或 APK，也未包含完整硬件原理图/PCB 工程、示例歌曲或自动化测试工程。** 两个子项目中保留的原始使用说明包含对更大交付包的描述，当前仓库的实际范围以本页和文件目录为准。

## 硬件与时钟

核心板为 Tang Nano 20K，目标器件 `GW2AR-LV18QN88C8/I7`，顶层模块 `play_lcd_top`。外设包括 480×272 RGB LCD、MAX98357A、触摸与 LED 扩展电路、ADS1115、SD 卡和 ESP8266。

工程要求 MS5351 CLK0 为 9 MHz、CLK1 为 24.576 MHz；约束分别为 111.111 ns、40.690 ns。完整管脚映射见 [硬件说明](docs/Hardware.md) 与工程 `.cst`。

## 构建与烧录

1. 使用 Gowin IDE 打开 `WuxianZhiQin_FPGA_v1.4_Orange_LCDfix/fpga/httm_sd_follow.gprj`，确认器件及顶层后执行综合、布局布线。在配套 Gowin 命令行环境中，也可于 `fpga` 目录执行 `gw_sh build.tcl`。
2. 用 Gowin Programmer 下载新生成的码流到对应板卡。`firmware/` 内为压缩包自带产物，未在本次整理时重新编译，也未验证它们与当前源码逐项一致；修改源码后应重新构建。
3. 先把草图目录的 `wifi_config.example.h` 复制为 `wifi_config.h`，填写自己的热点密码（至少 8 个字符）。Arduino IDE 安装 ESP8266 板卡支持，打开 `WuxianZhiQin_ESP8266_v1.4_Orange_MAB/esp8266_wifi_upload/esp8266_wifi_upload.ino`，保留同目录 `phone_assets.h`，按实际 ESP8266 板型和串口编译烧录。
4. 修改网页后，在草图目录运行 `python build_phone_assets.py`，再重新编译 ESP8266 草图。

依赖和详细步骤见 [构建说明](docs/Build.md)。原压缩包未锁定高云、Arduino IDE 或 ESP8266 Core 的精确版本；仓库不宣称已完成本次硬件构建或实机联调。

## 使用

连接设备热点 `FPGA-MUSIC`，密码由本地 `wifi_config.h` 设置，浏览器访问 `http://192.168.4.1/`。从 `wifi_config.example.h` 复制为 `wifi_config.h` 后填写自己的热点密码；该本地文件已被 `.gitignore` 排除。

S1 进入/确认菜单，S2 切换选项，双击 S1 返回。菜单包括 PLAY LIVE、SD MUSIC、FOLLOW PLAY、WI-FI UPLOAD。底板 SW1 短按切 A、长按切 B，SW2 切组合模式，SW3 短按切效果、长按切节拍提示。

SD 播放使用 FAT16/FAT32 根目录及英文 8.3 文件名，每类最多显示 8 首；上传要求 FAT32。封面使用 `WuxianZhiQin_FPGA_v1.4_Orange_LCDfix/fpga/SD_CARD/COVER.BMP`，格式为 480×272、4 位、16 色索引、未压缩 BMP。

## 架构与模块

```text
触摸 / ADC / 功能键 → FPGA 输入与控制 → DDS / 混音 / I²S → MAX98357A
SD 卡 ↔ FPGA 读卡 / 播放 / 跟弹 ──────→ LCD 与提示灯
手机网页 ↔ ESP8266 HTTP / FAT32 ↔ UART ↔ FPGA 控制与扇区桥
```

详见 [架构说明](docs/Architecture.md)。除热点密码改为本地配置外，源码、约束、封面及必要生成资源保持原内容；构建缓存、综合网表、临时日志及个人 IDE 设置未纳入仓库，`impl/` 中两份工程设置 JSON 已保留。

## 许可证

原压缩包未附带许可证，本次整理未代为选择开源许可证。仓库公开可见不代表自动授予修改和再分发许可；具体授权由项目权利人确定。
