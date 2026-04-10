---
title: Prompt 集合
date: 2025-12-08 19:54:30
tags:
  - 提示词工程
---

## 开源的提示词库

- https://www.promptingguide.ai/
- https://platform.claude.com/docs/zh-CN/build-with-claude/prompt-engineering/overview
- https://www.geeksforgeeks.org/artificial-intelligence/contextual-prompting/
- https://platform.openai.com/docs/guides/prompt-engineering
- https://www.anthropic.com/engineering/contextual-retrieval
- https://www.kaggle.com/whitepaper-prompt-engineering

## 万能提示词

```plaintext
我想......，如果你是一位专业人士，有更好的方法和建议吗？尽可能全面。
```

### 李开复先生的提示词

- 你是一个科学家，请你讲解给中学生听，什么是光和作用，一步一步来。
- 我要做台湾卤肉饭，请给我食材表格，实现步骤和做法。
- 阅读报告，paper
- 制作 PPT: 帮我制作一份 PPT，PPT 的内容是给小月 4 年级到 6 年级学生讲解什么是 AI

## 论文阅读

- by chatgpt

```plaintext
Read the given academic paper and produce a structured summary in Chinese.

Output requirements:

1. Idea.txt
- Length: 200–300 words
- Role: Domain expert
- Content: Core idea, key contributions, and significance

2. Abstract
- Length: 500–600 words
- Role: Domain expert
- Content: Background, methodology, results, conclusions

3. One page.txt
- Provide a one-page summary of the entire paper
- Then summarize each section/chapter in 500–600 words each

4. Output format
- Combine all parts into a single Markdown file
- File name must be identical to the original paper name
```

- by deepseek

```plaintext
Please read the attached paper and provide a comprehensive summary in Chinese, structured into three distinct parts. Save the final output as a Markdown file with the same name as the paper.

**Part 1: Idea.txt (200-300 words)**
Act as a domain expert. Provide a high-level overview of the entire paper, capturing the core motivation, the key novelty or insight, and the primary result. This should be a concise, big-picture summary suitable for a quick understanding of the paper's contribution.

**Part 2: Abstract Summary (500-600 words)**
Act as a domain expert. Write a detailed abstract of the full paper. Expand on the context, problem definition, methodology, experimental setup (if applicable), and main findings. This section should provide a thorough understanding of the paper without reading the full text.

**Part 3: One Page.txt (Per-Chapter Summary)**
Act as a domain expert. Summarize the paper chapter by chapter. Provide a summary of 500-600 words for **each** distinct chapter or major section of the paper. Combine all chapter summaries into a single section titled "One Page.txt."

**Output Format:**
Combine Part 1, Part 2, and Part 3 into a single Markdown file (`.md`). Name the file exactly the same as the paper's title (or the filename you provided). Use clear Markdown headers (`#`, `##`) to separate the three parts and the individual chapters.

---

**Key Improvements Made to the Prompt:**
- **Clarity:** Fixed grammatical issues ("read it and summary" -> "read it and provide a summary").
- **Structure:** Clearly delineated the three parts with explicit word count requirements.
- **Role Definition:** Explicitly stated "Act as a domain expert" at the start of each part to set the tone.
- **Specificity:** Added "motivation," "novelty," "methodology," and "experimental setup" as guidelines for what constitutes an expert summary.
- **File Naming:** Clarified the instruction regarding the output file name.
```

未完待续~
