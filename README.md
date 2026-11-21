# Slyztrix

> 把常用软件移植到 SaylterOS，让自研系统能承担普通的一天。

Slyztrix 是 [SaylterOS](https://github.com/RuanMingze/SaylterOS) 的软件生态项目。
它不重写内核，也不假装兼容 Windows 二进制：按可移植性分级，能搬的搬，
不能搬的用原生实现替代，并把每条限制写在明面上。

完整的项目宣言与路线图见 [pledge.html](pledge.html)。

## 状态

**BOOTSTRAP** —— 底座方案与首批清单已确定，尚未开始代码移植。

已有底座（来自 SaylterOS）：

- SlyzFS v2 文件系统与权限
- `.slys` 固定基址模块、`.slyzd` 动态链接、`SlysKernelApi`
- `GuiExternalHost` 窗口宿主与 SlyerType 16x32 字形
- E1000 + 自研 TCP/IP + TLS
- MP3 解码与音频驱动
- VizCormin 独立窗口化代码编辑器

## 第一优先级：musl libc

先移植 musl libc（MIT、轻量、静态链接友好），复用 stdio / malloc /
string / math / time，把移植成本收敛到系统调用适配：

- musl 静态链接：crt、TLS、stdio、malloc、string、math、time
- syscall 垫片：文件 / 内存 / 时间 / 退出，映射到 `SlysKernelApi`
- `libslyzposix` 只保留 OS 适配与 musl 缺失接口
- 线程先单线程 TLS，pthread 后续映射到协作任务
- 动态链接（ldso）后置，先静态链接进 `.slys`

## 移植分级

| 级别 | 内容 | 策略 |
| --- | --- | --- |
| L0 | C 运行时 | musl libc + syscall 垫片 |
| L1 | 纯计算库 | miniz、libpng、stb_image、SQLite、Lua，静态链接进模块 |
| L2 | 命令行工具 | zip/tar/gzip、grep/sed、iconv、sha256sum，原生实现优先 |
| L3 | 窗口应用 | 复用核心算法，界面重写为 SaylterOS 窗口 |
| L4 | 攻坚目标 | Electron 外壳 → JS 引擎 → Chromium/WebKit/Servo 渲染层 |

## 首批清单

**P0 日常闭环**：musl libc、GBK ↔ UTF-8、解压/打包、图片查看、
文件操作补齐、grep/sed、剪贴板。

**P1 生产力**：Markdown 预览、PDF 文本层、CSV 表格、
Electron 应用外壳、截图、输入法完善。

**P2 开发与扩展**：Git 本地子集、SlyzPkg 包管理器、Lua 运行时、
性能面板、移植模板。

## 里程碑

- **T0** musl 底座与模板
- **T1** 日常闭环
- **T2** 生产力
- **T3** 开发与生态（含浏览器内核攻坚启动）

## 原则

1. 不改内核换功能：只通过模块 ABI、SlyzFS 与窗口宿主接入。
2. 第三方代码不进内核镜像。
3. 每个移植必须有验收脚本：启动、绘制、退出、无泄漏。
4. 限制写在明面上：单地址空间、无 fork/exec、无 GPU 加速。
5. 保留上游版权与许可证，不把别人的成果伪装成自研。

## 目录

```
Slyztrix/
├── README.md       项目说明
└── pledge.html     项目宣言与路线图
```

## 许可证

项目自身许可证待定；移植代码各自保留上游许可证与版权声明。
