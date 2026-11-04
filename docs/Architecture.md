# 系统架构

## 数据流

```text
phone_source 网页 → ESP8266 HTTP 服务 → UART 帧协议 → FPGA 控制桥
触摸 / 音量 / FSR → I²C 采集 → play_core → I²S → MAX98357A
SD 卡 → SPI 读卡 → WAV/NTE 播放及跟弹 → 音频 / LCD / LED
ESP8266 FAT32 写入 → UART 扇区桥 → SPI SD 卡
```

FPGA 顶层 `play_lcd_top` 连接输入、音频、显示和 SD 通路。24.576 MHz 与 9 MHz 为异步时钟组；其关系以 `.sdc` 为准。ESP8266 管理网络请求和上传会话，FPGA 提供时序与扇区访问。

## RTL 模块索引

| 模块 | 作用 |
| --- | --- |
| play_lcd_top | 顶层实例连接、SD 通路选择及启动协调 |
| pcf8574_keys | I²C 触摸、ADC 与提示灯相关采集和控制 |
| play_core | 音符、DDS、音色、混音及效果处理 |
| follow_rhythm_score | 跟弹节奏与得分控制 |
| lcd_menu_keys | 菜单按键和界面状态 |
| lcd_menu_pixels | 菜单像素与状态文字 |
| lcd_cover_ram | 封面索引与调色板存储 |
| lcd_notes_480x272 | LCD 时序、画面选择与输出 |
| sd_spi_reader | SPI SD 底层读取 |
| sd_wav_player | 目录、播放、乐谱及封面读取 |
| phone_control_bridge | 手机控制命令、状态快照与界面数据 |
| upload_uart | 上传串口接收与发送 |
| upload_sd_bridge | 上传会话的 SD 扇区访问 |

## 上传接口边界

ESP8266 与 FPGA 的 UART 为 115200 8N1。草图的帧发送包含 `0xA5`、命令、长度、载荷及 CRC16，并等待 `0x06` 应答。上传期间由草图管理 FAT32 文件布局，FPGA 提供物理扇区访问，避免与正常 SD 播放同时驱动卡接口。命令定义及超时行为以 `esp8266_wifi_upload.ino` 与桥接 RTL 为准。
