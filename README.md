# Cercis（紫荆）

> 愿瑟希斯捍卫你的思想。 / *May Cerces safeguard your thoughts.*

**一个文件夹就是一份数据库。** Cercis 是一个本地优先的科研数据记录工具：
数据以纯 CSV 存在你自己的文件夹里，工具只负责把它变得**可靠、可追溯、AI 能读懂**。

没有云端、没有专有格式、没有锁定：不用这个软件，你也能用 Excel 打开、用 R 直接分析。

## 它想解决什么

| 常见困境 | Cercis 的做法 |
|----------|----------------|
| 数据分散在几十个 Excel 里，改错了查不出是谁改的 | **数据只增不改**：更正写一条更正记录，原值永久留存；每次改动都有流水（谁 / 哪台设备 / 何时 / 为什么） |
| 手写记录 → 誊抄 → 录入，错一处整表可疑 | 导入前**试运行**、逐行检查列名/必填/主键/引用完整性；有问题整批不写 |
| 统计分析前反复"清洗"，别人复现不出来 | 纯文本唯一事实来源 + 稳定编号 + 导出说明书，结果可回溯到某一版数据 |
| 想让 AI 帮忙取数、写脚本，却不敢让它碰文件 | AI 走**内核命令 + `--json`**：只读默认、写入留痕，绕不过校验 |
| 工具一停更，数据就打不开 | 文件夹即数据库：CSV + 图片 + Markdown，任何工具都能读 |

## 仓库一览

| 仓库 | 是什么 | 许可 |
|------|--------|------|
| **[cercis-core](https://github.com/epigrove/cercis-core)** | 内核：数据存取 + AI 友好（Rust 库 + `cercis` 命令行） | Apache-2.0 |
| **[cercis-kits-basic](https://github.com/epigrove/cercis-kits-basic)** | 基础工具包：项目向导 / 通用录入 / 通用导入导出 / 通用体检 | Apache-2.0 |
| **[cercis-kits-research](https://github.com/epigrove/cercis-kits-research)** | 科研工具包：科研模板、术语包、SPSS/R 导出、打印标签、NAS | Apache-2.0 |
| **[cercis-ui](https://github.com/epigrove/cercis-ui)** | 插件界面规范：一套界面图样，各平台搭成自己的原生界面 | Apache-2.0 |
| **[cercis-apps](https://github.com/epigrove/cercis-apps)** | 各平台界面与插件宿主（桌面本地网页界面先行） | Apache-2.0 |
| **[cercis-examples](https://github.com/epigrove/cercis-examples)** | 合成示例与黄金对照验收：生成器、全链路脚本、R 复算与报告 | Apache-2.0 |
| **[cercis-docs](https://github.com/epigrove/cercis-docs)** | **读者文档**：数据格式规范、模板说明、教程、合成示例导览 | CC BY 4.0 |

> 需求文档、决策记录等**开发内部文档不公开**（只在本地版本管理）；面向读者的
> 格式规范、教程与合成示例导览在 **cercis-docs**，且不含任何真实研究数据。

## 装什么、怎么装

| 装上 | 得到 | 怎么来 |
|------|------|--------|
| **Cercis app**（内核 + 基础工具包） | 建项目、录入、导入导出、体检、AI 友好（AGENTS.md / 契约 / 技能包） | **安装包就这一份**，装完即用 |
| 科研工具包 | 科研模板、术语包、SPSS/R 导出、打印标签、NAS、田间录入员 | **默认不装**：app 里预置了科研插件市场，想要时点安装 |
| 行业 / 第三方工具包 | 任意扩展 | 添加市场（git 仓库里的清单），或手动放进项目的 `plugins/` |

**所有插件规则一致**——包括随包的基础插件：都能停用、屏蔽、单独授权，也都能在安全模式下一键关掉。

## 架构：内核 + 插件

```
        ┌─────────────── app（各平台界面 + 插件总管）───────────────┐
        │                                                            │
   基础工具包（人人可用）          科研工具包（科研专用）
        │                                                            │
        └──────────────────── 内核（数据存取 · AI 友好）─────────────┘
```

- **内核不认识学科**：只认表、记录、字段、文件、历史；"研究/样本/观测"这类含义由插件模板提供，换模板就变成另一套系统（企业台账、库存、设备记录都行）。
- **写入只有一个通道**：任何插件、任何 AI 都必须通过内核命令写入——原子落盘、逐次存档、自动留痕。
- **app 是插件宿主**：负责发现插件、按声明的权限授权、以子进程调用；插件崩溃不影响主程序与数据。
- **插件界面只写一份**：插件交一份"界面图样"，各平台的 app 照图搭成自己的原生控件——结构一致，长相随系统（见 `cercis-ui`）。
- **AI 是使用者，不是特殊通道**：内核为 AI 生成 `AGENTS.md`、机器清单与契约，还能一键装一个 **Agent 技能包**（Codex / Claude Code 直接会用）。

## 现在到哪一步了

| 步骤 | 内容 | 状态 |
|------|------|------|
| S1–S3 | 内核：数据存取、模板与查询、AI 友好与插件协议 | **完成**（44 个测试全绿） |
| S4 | 桌面本地网页界面（打开 / 浏览 / 筛选 / 流水 / 校验，中英双语） | **完成** |
| S5 | 基础工具包第一批四个插件 + app 插件宿主 | **完成**（20 个测试，含跨进程端到端） |
| 附带 | AI Agent 技能包（与 AI 契约同源生成） | **完成** |
| 进行中 | **插件界面规范（cercis-ui）**：一套插件界面在各平台原生生效 | **v1 草稿**（规范 + 图样格式 + 校验器 + 两个官方示例） |
| S6 | 黄金对照验收：合成数据走完整链路 + R 复算 + 技能包 | **完成**（[72 项对账 0 失败](https://github.com/epigrove/cercis-examples/blob/main/verify/REPORT.md)；发现 6 处问题，1 处已修） |
| S7 | 科研工具包：**字段模板 + 术语包 + 方法文档** | **完成**（[田间试验模板 9 张表](https://github.com/epigrove/cercis-kits-research/blob/main/templates/field-trial.yaml)；农业/医学/通用术语包，切换术语**数据字节不变**） |
| S8–S9 | 科研工具包：导入导出（xlsx/SPSS/R） / 记录与同步（含田间录入员） | 待做 |
| S10 | 插件市场 + Zotero 式插件界面 | 待做（**科研工具包对外分发的门槛**） |

## 三分钟跑起来（合成数据，不涉及真实研究数据）

```bash
git clone https://github.com/epigrove/cercis-core.git
git clone https://github.com/epigrove/cercis-kits-basic.git
git clone https://github.com/epigrove/cercis-apps.git

cd cercis-core      && cargo build            # 内核与命令行
cd ../cercis-kits-basic && cargo build         # 四个插件
mkdir -p ~/.cercis/plugins && cp -r plugins/* ~/.cercis/plugins/
export PATH="$PWD/target/debug:$PATH"

cd ../cercis-apps/desktop
export CERCIS_BIN="$(cd ../../cercis-core && pwd)/target/debug/cercis"   # 告诉 app 内核在哪
cargo run                                      # 浏览器打开 http://127.0.0.1:8765
```

在界面里用「项目向导」新建一个项目，就能开始录数据、导入 CSV、导出与体检。

只想用命令行也可以：

```bash
cd ../cercis-core
./target/debug/cercis init ~/我的项目 --name "我的项目" --json
./target/debug/cercis skill ~/我的项目 --install codex   # 让 Codex 学会怎么操作这个项目
```

## 红线（不可动摇）

1. **发布零数据**：任何发布物（仓库、安装包、分享包）都不含真实研究数据。
2. **不擅动已有文件夹**：对既有文件夹只提供"挂载（只读索引）"与"移动（明确确认后迁入）"。
3. **数据只增不改**：更正等于新增更正记录，原始记录永久保留。
4. **内核保持行业普适**：不做学科专用内核，科研能力由工具包提供。

## 关于名字

三个名字各司其职：

| 名字 | 用在哪 | 是什么 |
|------|--------|--------|
| **Cercis** | 产品名 | 紫荆属的学名——用真实植物属名，专业场合不尴尬，检索不会被同名游戏内容淹没 |
| **Cerces** | 标语 | 「愿瑟希斯捍卫你的思想」致敬《崩坏：星穹铁道》中的瑟希斯——**非官方、非商业用途** |
| **epigrove** | 组织名 | Epiphany Grove（「神悟树庭」）的短名——epigrove 好念好打，又留着 grove 的意象 |

换成一句话：**产品名保持中立，致敬放在标语，归属放在组织名。**
这样既认得出典故，又不会把游戏专有名词变成要承担商标风险的品牌
（这也是为什么没有直接叫 `Epiphany`——那个名字在 npm、PyPI、crates.io 上也都已经被占了；
组织名也从 `epiphanygrove` 缩短成了 `epigrove`。）

## 许可

代码 Apache-2.0；文档、模板与合成示例 CC BY 4.0。

---

<details>
<summary>开发工作区（本目录）</summary>

本目录是开发工作区，每个子目录是一个独立仓库，分别对应上面表格中的项目：

```
Cercis/
├── kernel/          → cercis-core
├── kits/basic/      → cercis-kits-basic
├── kits/research/   → cercis-kits-research
├── apps/            → cercis-apps
└── docs/            需求文档等内部文档（不公开）
```

发布红线：任何发布物不含真实研究数据；分享包默认排除 `plugins/` 与 `access/`。
</details>
