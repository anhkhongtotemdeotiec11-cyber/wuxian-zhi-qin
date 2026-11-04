# 目录说明

```text
wuxian-zhi-qin/
├── README.md
├── .gitignore
├── .gitattributes
├── docs/
│   ├── Architecture.md
│   ├── Build.md
│   ├── Hardware.md
│   └── DirectoryStructure.md
├── firmware/                     # 原包已有的 FPGA 码流
├── WuxianZhiQin_FPGA_v1.4_Orange_LCDfix/
│   ├── README_使用说明.md         # 原始版本说明
│   └── fpga/
│       ├── httm_sd_follow.gprj
│       ├── build.tcl
│       ├── constraints/
│       ├── impl/                 # 仅保留工程设置 JSON
│       ├── rtl/
│       ├── SD_CARD/
│       └── tools/
└── WuxianZhiQin_ESP8266_v1.4_Orange_MAB/
    ├── README_使用说明.md         # 原始版本说明
    └── esp8266_wifi_upload/
        ├── esp8266_wifi_upload.ino
        ├── phone_assets.h
        ├── build_phone_assets.py
        └── phone_source/
```

源码目录沿用原包名称，保留高云工程内的相对路径。编译临时目录和个人 `.gprj.user` 未纳入版本库。三份原有 FPGA 固件单独存放，便于区分源码与交付产物。
