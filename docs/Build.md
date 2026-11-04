# 构建与下载

## FPGA

工程：`WuxianZhiQin_FPGA_v1.4_Orange_LCDfix/fpga/httm_sd_follow.gprj`。目标器件：`GW2AR-LV18QN88C8/I7`；顶层：`play_lcd_top`；Verilog 标准：2001。`impl/project_process_config.json` 与 `impl/httm_sd_follow_process_config.json` 是保留的工程设置。

在 Gowin IDE 中打开工程并重新执行综合、布局布线。也可在配置了 Gowin 工具路径的命令行中进入 `fpga` 目录运行 `gw_sh build.tcl`。下载前核对器件、约束、时钟和板卡连线；使用 Gowin Programmer 按实际下载器和 SRAM/Flash 目标操作。

`firmware/` 中三份码流来自原压缩包，不代表本次重新构建的结果。精确工具版本、源码与码流的一致性和硬件时序均需由实际构建确认。

## ESP8266 与网页

依赖 Arduino IDE 与 ESP8266 Arduino Core。草图使用 `ESP8266WiFi.h`、`ESP8266WebServer.h` 及同目录 `phone_assets.h`；前两个由板卡支持包提供。

先将草图目录中的 `wifi_config.example.h` 复制为 `wifi_config.h`，填写自己的热点密码（至少 8 个字符）；本地配置不会提交到 Git。

打开 `WuxianZhiQin_ESP8266_v1.4_Orange_MAB/esp8266_wifi_upload/esp8266_wifi_upload.ino`，按实际硬件选择开发板和串口并上传。串口使用 `Serial.swap()` 后的 GPIO15 TX、GPIO13 RX，115200 8N1。板型和 Core 精确版本在压缩包中未锁定。

修改 `phone_source/` 内网页后，在草图目录执行：

```sh
python build_phone_assets.py
```

脚本以 Python 标准库 gzip 把四份网页资源写入 `phone_assets.h`。重新编译并烧录 ESP8266 后生效。仓库不包含生成 LCD 手机绘图器上游所需的 Android/转换工程；保留的 `renderer.js` 和 `worker-source.js` 可直接供此打包脚本使用。

## SD 卡工具

`fpga/tools/` 包含 `make_cover.py`、`make_nte.py`、`wav_to_nte.py`。先以 `python 脚本名 --help` 查看参数；图片工具使用 Pillow，其他依赖以脚本 import 为准。SD 上传要求 FAT32，歌曲需符合英文 8.3 命名与支持格式。

## 本次整理验证

验证范围为文件完整性、工程引用路径、Python 工具语法与网页压缩资源一致性检查。未执行 Gowin/Arduino 编译、烧录或实机测试。
