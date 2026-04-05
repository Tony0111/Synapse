# Doc_Daway: A Verifiable Medical Question-Answering Agent

> 中文版本：[README.md](./README.md)

## Project Introduction

Doc_Daway is a medical education-focused question-answering project. This project aims to make AI reasoning processes explicit, enabling reasoning to be falsifiable and thereby establishing trustworthiness. This leads to a fundamental question: **In Medical RAG systems, is answer accuracy (accuracy) alone sufficient to evaluate output quality?**

This project is built on an Obsidian local knowledge base, utilizing structured atomic notes, YAML metadata indexing, and bidirectional linking mechanisms to implement an explicit causal-chain reasoning (Causal-Chain Reasoning) workflow. Rather than pursuing higher answer accuracy, this project explores whether system output's **Evidential Traceability** and **Reasoning Inspectability** deserve to be considered as independent observation dimensions in the context of medical education.

**Core Positioning**: This is an exploratory case study project, not primarily aimed at performance improvement, but rather at observing differences in reasoning structure transparency among different systems when answer accuracy is similar.

This project represents an exploration of medical education question-answering based on structured local knowledge base and Causal-Chain Workflow
Related research paper: *Rethinking Accuracy in Medical RAG: A Case Study with Causal-Chain Reasoning* (Incomplete)

## Core Research Questions

The core of this research is to explore evaluation dimensions of Medical RAG in medical education scenarios, focusing on the following three research questions:

**RQ1**: Does accuracy-centric evaluation approach have limitations in Medical RAG systems?
**RQ2**: Do question-answering outputs in medical education require more explicit evidential traceability and reasoning inspectability?
**RQ3**: When answer correctness and reasoning inspectability are inconsistent, how should we understand system performance?

In an undergraduate pathology (inflammation chapter) question-answering scenario, our preliminary observations show:
- Traditional RAG and general LLM interfaces perform similarly in terms of answer accuracy
- However, there are significant differences in the explicitness of reasoning processes, precision of evidence source mapping, and inspectability of intermediate steps
- Answer correctness does not necessarily imply transparent reasoning processes; conversely, transparent reasoning chains help locate specific points where errors occur

These observations form the empirical foundation for the related research paper *Rethinking Accuracy in Medical RAG: A Case Study with Causal-Chain Reasoning* (Incomplete).

## Core Technical Implementation

### Structured Local Knowledge Base

- **57 Atomic Notes**: Based on the inflammation chapter of pathology textbooks, following the "one note, one concept" principle to ensure knowledge unitization
- **YAML Metadata Fields**: Include structured fields such as `id`, `category`, `keywords`, `tags`, supporting precise semantic retrieval rather than just keyword matching
- **Bidirectional Linking Network**: Establish causal and associative paths between concepts through outgoing/incoming links (see [Bidirectional Linking Table](./双向链接表.md))
- **Obsidian Management**: The knowledge base uses Obsidian for local organization and visual presentation

### Causal-Chain Reasoning Workflow

The workflow operates based on **Claude Code** calling the **SiliconFlow API** (Pro/deepseek-ai/DeepSeek-V3.2), implementing the following core stages:

1. **Question Parsing**: Extract core medical entities and reasoning objectives
2. **Structured Retrieval**: Match relevant atomic notes based on YAML fields
3. **Evidence Extraction**: Read note content to establish evidence pools
4. **Preliminary Reasoning**: Construct Stepwise Reasoning Chain
5. **Self-Review**: Check evidence sufficiency and logical consistency (critical self-review)
6. **Deep-Link Expansion**: Expand evidence scope through iterative retrieval along bidirectional links
7. **Iteration Stopping Conditions**: Stop when confidence reaches threshold (0.85) or reaches maximum iterations (5 times)

![Workflow Diagram](Latex/images/fig2.svg)

Workflow definition details: `.claude/workflows/medical_reasoning.yaml`

### Metadata Retrieval System

This project employs multi-dimensional metadata retrieval rather than simple keyword matching. Complete retrieval field definitions: [`YAML.md`](./YAML.md)

**Core Field Design**:
- `category`: Inflammation, Degeneration, Exudation, Proliferation (lesion nature classification)
- `type`: Pathological processes, Pathological changes, Pathogenesis, Etiology... (knowledge type classification)
- `keywords`: 200+ pathology professional terms (precise retrieval)
- `tags`: 30+ topic labels (topic aggregation)

### Output Format

The core characteristic of system output is **explicit reasoning processes**, containing the following structured content:

- **Final Answer**: Clearly responding to user questions
- **Stepwise Reasoning Chain (Reasoning Chain)**: Demonstrating the step-by-step reasoning process from evidence to conclusions
- **Atomic Note-Level Evidence Citation (Note-level Citation)**: Precise source referencing using `[[Path-XXX|Note Title]]` format
- **Confidence Assessment**: Confidence score (0-1) based on evidence sufficiency and logical consistency
- **Iteration Count**: Recording the number of cyclic retrieval operations during reasoning

![Output Example](images/输出示例.png)

This output design enables every conclusion to be traced back to specific knowledge sources, allowing reasoning processes to be independently reviewed.

## Performance Comparison

The following shows three system outputs for the same sample question:

### Question

![Comparison Question](images/对比_问题.jpg)

### Doc_Daway

![Doc_Daway Output](images/对比_causal_chain.jpg)

### Conventional RAG

![Traditional RAG Output](images/对比_conventional_rag.jpg)

### DeepSeek Web

![DeepSeek Web Output](images/对比_deepseek_web.jpg)

**Key Observation**: With similar answer accuracy (Traditional RAG 97.3% vs Doc_Daway 94.7% vs DeepSeek 96.5%), Doc_Daway's output structure more clearly demonstrates evidence dependency relationships and reasoning jump points, making intermediate steps independently reviewable.

## Experimental Results Preview

| System                 | Correct / Total | Accuracy |
|------------------------|----------------|----------|
| Conventional RAG       | 110 / 113       | 97.3%    |
| Causal-Chain Workflow  | 107 / 113       | 94.7%    |
| DeepSeek Web Interface | 109 / 113       | 96.5%    |

**Important Note**: In this pathology question-answering case, all systems show relatively high final answer accuracy. This project presents this data **not to emphasize performance superiority**, but to illustrate that when answer accuracy differences are limited, structural differences in evidential traceability and reasoning inspectability may still be worth attention.

The Causal-Chain Workflow has the lowest accuracy, partly because this experiment's knowledge base contains only knowledge from one chapter of pathology. Doc_Daway heavily relies on the knowledge base for reasoning, resulting in significant errors for knowledge not contained in the knowledge base.

## Repository Content Description

To balance reproducibility with copyright/research strategy, this repository adopts a tiered openness approach:

### Publicly Available

- **Workflow Code**: `.claude/workflows/medical_reasoning.yaml` (Causal-Chain workflow definition)
- **Comparison Example Figures**: `images/`
- **Knowledge Base Samples**: `kb_template/` (Atomic note examples demonstrating knowledge structure, excluding full textbook content)
- **Project Documentation**: `README.md` (Chinese version), `README_en.md` (English version), `CLAUDE.md` (project configuration instructions)

### Partially Available (Sample Forms)

- **Knowledge Base Content**: Only 3-5 representative atomic notes as structural examples; complete 57 notes not fully publicly available due to copyright restrictions
- **Test Questions**: Only a few example questions provided; complete 113 question set from third-party platform (医考帮) not fully included due to copyright protection [Questions_public](./Questions_public.md)

### Not Publicly Available

- **Incomplete Paper**: `paper.md` / `paper.pdf` (Markdown and PDF versions)

---

## How to Run / How to Reproduce

This is a concise reproduction path to help you run Doc_Daway's Causal-Chain Workflow in your local environment.

### Environment Dependencies

- **[Claude Code](https://claude.ai/code)**: Command-line tool (workflow execution environment)
- **SiliconFlow API Key**: For accessing DeepSeek-V3.2 model (author tested with Pro/deepseek-ai/DeepSeek-V3.2 model)
- **Obsidian** (optional): For viewing knowledge base structure and bidirectional links

### Core Entry Points and File Locations

- **Workflow Definition**: `.claude/workflows/medical_reasoning.yaml`
- **Knowledge Base Samples**: `kb_template/` (note examples)
- **Metadata Definition**: `YAML.md` (complete retrieval field list)

### Configuration and Execution Process

1. **Configure API Key**
2. **Start Workflow** (from project root directory)
   ```text
   @.claude/workflows/medical_reasoning.yaml
   Follow this workflow to answer my question:
   ……
   ```

### Output Viewing Location

- **Terminal Output**: Structured answers displayed directly in terminal after workflow execution
- **Output Contains**:
  - Final answer
  - Stepwise reasoning chain (Reasoning Chain)
  - Atomic note-level evidence citation (using `[[Path-XXX|Note Title]]` format)
  - Confidence assessment and iteration count

### Note: Knowledge Base Limitations

Since the complete knowledge base (57 atomic notes) is not fully publicly available due to copyright restrictions, reproduction requires:
1. Building test atomic notes based on templates provided in `kb_template/`
2. Or contacting the author for limited academic exchange datasets

---

## Project Boundaries and Important Notes

Before using this project, please understand the following limitations:

1. **Non-Performance-Optimization Oriented**: This project does not claim to surpass existing RAG systems in accuracy (in fact, in this case, Doc_Daway's accuracy is slightly lower than traditional RAG). Its core contribution lies in proposing **reasoning transparency** as a supplementary evaluation dimension.

2. **Case Study Nature**: All observations are based on 113 questions from a single pathology chapter (inflammation); conclusions should not be directly generalized to broad medical question-answering.

3. **Explicit Reasoning ≠ Correct Reasoning**: Explicit causal chains facilitate inspection but do not guarantee the correctness of reasoning content. Transparency is a tool for **inspectability**, not a certificate of **correctness**.

4. **Copyright Limitations**: The knowledge base is organized based on textbooks and is protected by copyright; this repository only demonstrates methods and structure, complete data requires acquisition through formal educational channels.

## Contact and Discussion

Welcome to discuss the following topics through GitHub Issues:

- RAG evaluation metric design in medical education scenarios
- Improvement suggestions for Causal-Chain Workflow
- Atomic note improvement design

Email: tonyxu0111@gmail.com