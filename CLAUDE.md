# Doc_Daway - 医学知识问答智能体

## 项目概述

Doc_Daway 是一个基于 Obsidian 本地知识库的医学知识问答智能体，借助双向链接机制和 YAML 标签检索，最大限度地减少 AI 幻觉，增强回答的医学准确性。

## 核心文件

| 文件 | 说明 |
|------|------|
| `Pathology/` | 病理学知识库（57个笔记文件） |
| `双向链接表.md` | 所有笔记的双向链接关系 |
| `YAML.md` | 笔记 YAML 字段及所有对应值 |

## 工作流

- `.claude/workflows/medical_reasoning.yaml` - 医学问答工作流

### 工作流核心流程

**理解 → 检索 → 推理 → 审查 → （如需）深入链接 → 迭代**

1. **request_parsing** - 解析医学题目，提取关键信息
2. **keyword_extraction_and_search** - 阅读 YAML.md 后，在笔记中检索
3. **initial_reading_and_reasoning** - 阅读笔记，提取关键信息
4. **preliminary_answer** - 生成初步答案
5. **self_review** - 批判性自我审查（5项检查）
6. **branch_on_review** - 条件分支处理
7. **deep_link_exploration** - 沿双向链接深入阅读
8. **iteration_loop** - 迭代循环控制

### 工作流配置

- 最大迭代次数：5
- 置信度阈值：0.85
- 最少证据数：2条

## 目录结构

```
D:\Synapse\
├── Pathology/                 # 病理学笔记库（57个笔记）
├── CLAUDE.md                # 本配置文件
├── YAML.md                  # YAML 字段与值参考
├── 双向链接表.md             # 双向链接关系表
└── .claude/
    └── workflows/
        └── medical_reasoning.yaml  # 医学问答工作流
```

## 笔记检索

检索笔记时，请先阅读 `YAML.md` 了解可用的：
- **keywords**：200+ 个关键词
- **tags**：30+ 个标签
- **category**：炎症、变质、渗出、增生
- **type**：病理过程、病理改变、发病机制、病因学、鉴别诊断、临床表现、疾病分类、疾病

## 双向链接

笔记之间的双向链接关系详见 `双向链接表.md`，包含：
- 出链（应指向）
- 入链（应被指向）
- 知识逻辑结构图