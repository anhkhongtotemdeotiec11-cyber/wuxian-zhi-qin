# 硬件与管脚

目标为 Tang Nano 20K / GW2AR-LV18QN88C8/I7，外接 ESP8266、RGB LCD、MAX98357A、触摸/LED 扩展、ADS1115 与 SD 卡。以下编号均为 FPGA 器件管脚号，不是外部排针序号。

MS5351 CLK0 需设置为 9 MHz，CLK1 为 24.576 MHz。UART 使用 115200 8N1；ESP8266 GPIO15 为 TX、GPIO13 为 RX。SW1/SW2/SW3 对应草图 GPIO5/GPIO4/GPIO14。连接时交叉 TX/RX，并按实际电路确认电平转换与共地。

| FPGA 信号 | 器件管脚 |
| --- | --- |
| `clk9m` | 10 |
| `clk24m` | 11 |
| `btn_s1` | 88 |
| `btn_s2` | 87 |
| `uart_rx` | 71 |
| `uart_tx` | 72 |
| `sd_cs_n` | 81 |
| `sd_mosi` | 82 |
| `sd_sck` | 83 |
| `sd_miso` | 84 |
| `i2c_scl` | 73 |
| `i2c_sda` | 74 |
| `led_bus_n` | 19 |
| `led_key_n` | 20 |
| `pa_en` | 51 |
| `i2s_din` | 54 |
| `i2s_lrclk` | 55 |
| `i2s_bclk` | 56 |
| `lcd_bl` | 49 |
| `lcd_de` | 48 |
| `lcd_clk` | 77 |
| `lcd_r[0]` | 42 |
| `lcd_r[1]` | 41 |
| `lcd_r[2]` | 40 |
| `lcd_r[3]` | 39 |
| `lcd_r[4]` | 38 |
| `lcd_g[0]` | 37 |
| `lcd_g[1]` | 36 |
| `lcd_g[2]` | 35 |
| `lcd_g[3]` | 34 |
| `lcd_g[4]` | 33 |
| `lcd_g[5]` | 32 |
| `lcd_b[0]` | 31 |
| `lcd_b[1]` | 30 |
| `lcd_b[2]` | 29 |
| `lcd_b[3]` | 28 |
| `lcd_b[4]` | 27 |

全部 IO 类型和上拉/驱动配置以 `constraints/play_lcd.cst` 为准。I²C 扩展器地址与实际连线在顶层注释中标为 U4=0x38、U5=0x39。压缩包不含完整硬件原理图与 PCB 工程，不能仅凭本管脚表替代硬件连接核查。
