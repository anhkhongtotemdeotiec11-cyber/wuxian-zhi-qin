# 无弦之琴 · 1.4 暖橙分区版

在上一版暖橙 SW 版基础上仅调整状态显示与 M1 音色分区，继续使用八声部，不扩展到 32 路。原来的 SO/HG 标签、OPEN APP ON PHONE、SW 按钮与暖橙界面保留。

## 本次两项修改

1. LCD、APP 和网页镜像统一按“模式 M → 音色 A → 音色 B”显示，例如 `M1 AT BS`。演奏、SD 播放及跟弹页面中已有的音色状态文字均采用相同顺序。
2. M1 改为：上排 SO/LA/SI/HG 使用 A，下排 DO/RE/MI/FA 使用 B。实体琴键、手机琴键以及播放/跟弹时的实时演奏共用这套声部选择逻辑。

| 组合模式 | 本版实际音色分配 |
| --- | --- |
| M0 | 全部琴键使用 A，仍可演奏和弦 |
| M1 | 上排四键使用 A，下排四键使用 B |
| M2 | 每个音符混合 A+B |

APP/网页的“音色、效果与演奏控制”面板也将组合方式放在 A、B 前面，并标明 M1 的上下排分配。SW1 短按切 A、长按切 B；为避免两者相同，必要时会自动调整另一组。

## SW1 / SW2 / SW3

| 按钮 | 短按后松开 | 长按约 650 ms 后松开 |
| --- | --- | --- |
| SW1 · 音色 | 切换 A 音色 | 切换 B 音色 |
| SW2 · 组合 | 循环 M0/M1/M2 | 同样只切换一次组合 |
| SW3 · 效果 | 关闭→颤音 V→短回声 D→关闭 | 开关 120 BPM 节拍提示 |

长按期间按钮变色，在松开时执行一次；取消触摸或滑出按钮不会执行。连续点击按队列逐次读取硬件新状态后计算，避免重复使用旧参数。上传会话锁定时不调节。

这些按钮复用已有音色/效果 API，与底板的短按/长按规则一致。FPGA 没有新增 SW 控制模块，ESP 控制草图和 Android Java 都未改。底板功能键继续可用。

## 版本与源码范围

Android 安装版本码为 11，版本名 `1.4-orange.3`，可覆盖旧 APP。欢迎页排版继续沿用当前 1.4 基础版。
本次 FPGA 仅修改 `lcd_menu_pixels.v` 的状态文字顺序和 `play_core.v` 的 M1 声部音色选择条件。M0/M2 混音、音符频率、包络、音效、SD、串口、按键输入、管脚约束和高云编译参数沿用上一版。

## 清理内容

删除未被实例化的旧 `instrument_ctrl_uart.v` 及其工程引用、测试 WAV、示例 NTE、两个测量脚本、重复封面备份、旧 APK/预览/历史元数据和重复安装说明。交付包不含编译中间文件、测试程序或日志。

保留真正使用的 RTL、高云工程设置、管脚/时钟约束、SD 封面、格式转换工具、APP 工程与签名、网页源文件及必要生成资源。
`phone_assets.h`、`renderer.js`、`worker-source.js` 是编译或运行必需的资源；`impl` 下两个 JSON 是工程设置，不能当旧构建产物删除。

删除未使用文件主要是整理工程；这些代码原本可能已被综合工具优化掉，因此不保证 LUT/BSRAM 会因文件数量减少而明显下降。

## 分开安装

1. **FPGA**：解压 FPGA 包到新文件夹，使用高云打开 `fpga/httm_sd_follow.gprj`。器件 `GW2AR-LV18QN88C8/I7`，顶层 `play_lcd_top`。重新综合、布局布线，再下载新生成的码流。
2. **ESP8266**：Arduino IDE 打开 `esp8266_wifi_upload/esp8266_wifi_upload.ino`，同目录 `phone_assets.h` 一起保留，选择原来的板型和端口编译上传。草图控制代码与上一版逐字一致，更新的是网页资源。
3. **安卓**：安装 `WuxianZhiQin_1.4_Orange_MAB.apk`，可覆盖原 APP。
4. **完整源码**：包含上述源码与 APK，可用 Android Studio 打开 `android-src`。单独 FPGA 包不混入 ESP 草图；单独 ESP 包不含 FPGA RTL。

本包不提供未经高云编译的新 FPGA `.fs/.bin`，也不提供本次 ESP 交叉编译固件。

## 使用方法

热点 `FPGA-MUSIC`，密码由本地 `wifi_config.h` 设置，网页地址 `http://192.168.4.1/`。提示无互联网时保持连接。
APP 模式一同步 LCD；模式二自动进入 PLAY LIVE，支持八键及多指和弦。声音由开发板扬声器产生。

上电显示 SD 封面，S1 进入菜单，S2 换选项，S1 确认，双击 S1 返回。上排 SO/LA/SI/HG，下排 DO/RE/MI/FA。
菜单依次为 PLAY LIVE、SD MUSIC、FOLLOW PLAY、WI-FI UPLOAD。

| 底板操作 | 功能 |
| --- | --- |
| SW1 短按 / 长按松开 | 切换 A / B 音色 |
| SW2 | 循环 M0、M1、M2 |
| SW3 短按 / 长按松开 | 关闭→颤音→短回声 / 开关节拍提示 |

长按约 650 ms 后松开。底板 SW1/SW2/SW3 与 Tang 的 S1/S2 菜单键不同。
T/S/P 是三角、正弦、脉冲；M0 全部使用 A，M1 上排 A/下排 B，M2 每键 A+B。
1.4 原有的跟弹、WAV/NTE/NTE2、FSR、音量旋钮/手机接管、颤音深度、上传校验和超时松键功能保留。

## SD 卡

播放使用 FAT16/FAT32 根目录，歌曲用英文 8.3 文件名，例如 `SONG1.WAV`、`TIGER.NTE`；每类目录最多显示 8 首。上传要求 FAT32，不覆盖同名文件。没有新增中文歌名显示。
封面仍是 `COVER.BMP`：480×272、4 位、16 色索引、未压缩 BMP。可以继续使用现有暖橙封面。
已有歌曲无需重新制作；保留在卡上即可。格式转换工具位于 `fpga/tools`。

## 重新生成界面资源

1. `python tools/transpile_renderer.py`：从 LCD RTL 生成手机像素程序。
2. `python tools/generate_assets.py`：生成 Worker 并更新 Android assets。
3. 将生成的 `phone-ui/renderer.js` 和 `phone-ui/worker-source.js` 复制到 `fpga/esp8266_wifi_upload/phone_source`，保留网页自己的 index.html 和 app.js。
4. 在 ESP 草图目录运行 `python build_phone_assets.py`，更新压缩网页。
5. 编译 Android/ESP，并用高云重新编译 FPGA。

APP 命令行构建可使用 `python tools/build_android.py --sdk 你的SDK目录`，需要 JDK 17 与 Android SDK 35；也可用 Android Studio。签名文件保留以便后续升级。

## 验证与边界

本次验证包括 FPGA 完整顶层语法与模块编译、三种模式全部琴键的音色分配检查、三处状态文字的字符/像素检查、RTL 与手机整屏像素对照、APP 构建与签名以及 ESP 压缩网页资源一致性检查。

本环境没有运行高云最终综合布局布线，也未进行实际设备联调，不宣称解决旧版 PR0004。

必须重烧本包 FPGA 才能改变实际发声音色分区与实体 LCD 的文字顺序；仅更新 APP 不会改变 FPGA 内部的 M1 分配。
新 APK 更新手机镜像及面板说明，可覆盖旧 APP；使用网页版时需同步更新 ESP 草图目录中的网页资源。ESP 控制草图和通信协议没有改变，单独使用 APP 可沿用配套的原 ESP 控制固件。
