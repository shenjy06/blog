---
title: Claude Code 配置指南
date: 2026-02-18 19:54:30
tags:
  - claude code
  - vibe coding
---

> https://www.runoob.com/claude-code/claude-code-tutorial.html
>
> https://code.claude.com/docs/en/overview

## Claude Code 安装

> https://www.runoob.com/claude-code/claude-code-install.html

### `.claude.json` 文件内容变更

```json
{
  "env": {
    "ANTHROPIC_BASE_URL": "https://api.siliconflow.cn",
    "ANTHROPIC_API_KEY": "api-key",
    "ANTHROPIC_MODEL": "你希望默认使用的模型名",
    "ANTHROPIC_DEFAULT_SONNET_MODEL": "你希望默认使用的模型名"
  },
  "hasCompletedOnboarding": true
}
```

## 命令行启动注意问题

```shell
claude --model Pro/zai-org/GLM-5
```

## Claude Code 相关文章

- [Claude Code 技巧分享](https://shenjy0.substack.com/p/claude-code)
- [Launch Claude Code with Claude Code Router](https://shenjy0.substack.com/p/launch-claude-code-with-claude-code)

## 其他同类产品

- https://openai.com/zh-Hans-CN/codex/
- https://opencode.ai/zh
- gemini cli
- Kimi K2 Cli
- Qwen Code
- Rovo Dev CLI
