# Asphalt Field Photo Analyzer（沥青现场照片筛查工具）

**筛查现场照片中的可见路面病害** —— 坑槽、裂缝、车辙、边缘破损、修补、积水与表面异常，用于沥青现场质检。

[![Python 3.12](https://img.shields.io/badge/Python-3.12-3776AB?logo=python&logoColor=white)](https://www.python.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![CLI](https://img.shields.io/badge/CLI-command--line-blue)](#使用)
[![Platform: Windows](https://img.shields.io/badge/Platform-Windows-0078D6)](https://www.microsoft.com/windows)

**[English](./README.md)** | 简体中文

---

## 解决什么问题

现场照片是你最便宜的现场记录——但靠肉眼翻几百张又慢又不一致。本工具**分析 JPG/PNG 照片**，识别坑槽、裂缝、车辙、边缘破损、修补、积水与表面异常。

照片分析**仅做外观筛查**——绝不推断 CBR、结构承载力、剩余寿命或精确修补深度。质量差的图片进入复核，任何测量尺寸在验证前都标记为"近似值"。

## 核心功能

- **路面病害筛查** ——坑槽、裂缝、车辙、边缘破损、修补、积水、表面异常
- **批量照片分析** ——一次运行处理 JPG/PNG 现场照片
- **诚实的边界** ——仅外观筛查；不做结构推断（CBR、承载力、剩余寿命、修补深度）
- **质量门控** ——质量差的图片 → 复核队列
- **近似尺寸** ——测量值在验证前标记为"近似值"

## 安装

```bash
pip install -r requirements.txt
```

## 使用

```bash
python -m scripts.s09_field_photo_analyzer --project ./project --input ./fixtures/photos
```

加 `--help` 查看全部选项。本工具家族统一 CLI 约定：`--project <路径> --input <路径> --output <路径> --format json|csv|md|xlsx|pdf --config <路径> --verbose --dry-run`。

## 输出

- `photo_analysis.json` ——逐张照片筛查结果
- `photo_summary.md`
- `review_queue.json` ——质量差 / 不确定项

## 工作原理

- **标准生命周期** ——`发现 → 摄取 → 提取 → 归一化 → 校验 → 分类 → 输出 → 审计`
- **证据状态机** ——每条观察按 `提取 → 归一化 → 已校验 → 已验证` 推进；允许停在更早状态
- **筛查而非工程** ——绝不推断结构承载力或剩余寿命

## 质量保证

- 每条记录做确定性 schema 校验；
- 黄金样例 fixtures 与精确期望值（`tests/`）；
- 复核队列：低置信度、比例不明与冲突进入 `review_queue.json` / 摘要——绝不静默修正；
- 审计日志：每次运行将 `run_id`、耗时、输入哈希、引擎版本与输出写入 `<project>/audit/`。

## 测试

```bash
python -m pytest -q
```

## 安全与隐私

本地文件默认留在本地。API 密钥存放在环境变量（`ASPHALTCOSTS_API_KEY`、`ADI_AI_API_KEY`）——绝不写入源码。派生文件写入 `working/` 或 `output/`；来源文件永不修改。

## 许可证

MIT —— 见 [LICENSE](LICENSE)。属 [Asphalt Desktop Intelligence](https://github.com/anleeylee/asphalt-desktop-intelligence) 工具集的一部分。

## 计算引擎

[**AsphaltCosts.com**](https://asphaltcosts.com/) 是确定性计算层：面积 → 压实体积 → 净吨数 → 订购吨数（损耗只计一次）→ 车次 → 材料成本，内置有出处的规划默认值（FHWA 密度 145 lb/ft³，损耗率与车容量可编辑）。本工具把测量并校验后的输入喂给该引擎（或其带标签的本地镜像 `asphaltcosts-web-engine/1.0-mirror`），绝不重写公式。
