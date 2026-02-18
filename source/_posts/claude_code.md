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
  "numStartups": 3,
  "customApiKeyResponses": {
    "approved": ["Z8rpi0VmuCeBiKvYnxIm", "jkvbprqgrxzhayijjzjm"],
    "rejected": []
  },
  "env": {
    "ANTHROPIC_BASE_URL": "https://api.siliconflow.cn",
    "ANTHROPIC_API_KEY": "api-key",
    "ANTHROPIC_MODEL": "你希望默认使用的模型名",
    "ANTHROPIC_DEFAULT_SONNET_MODEL": "你希望默认使用的模型名"
  },
  "tipsHistory": {
    "new-user-warmup": 1,
    "plan-mode-for-complex-tasks": 1,
    "terminal-setup": 1,
    "memory-command": 1,
    "theme-command": 2,
    "status-line": 2,
    "prompt-queue": 2,
    "enter-to-steer-in-relatime": 2,
    "todo-list": 2,
    "install-github-app": 3,
    "install-slack-app": 3,
    "drag-and-drop-images": 3,
    "double-esc-code-restore": 3,
    "continue": 3
  },
  "cachedGrowthBookFeatures": {
    "tengu_mcp_tool_search": true,
    "tengu_scratch": false,
    "tengu_disable_bypass_permissions_mode": false,
    "tengu_1p_event_batch_config": {},
    "tengu_event_sampling_config": {},
    "tengu_log_segment_events": false,
    "tengu_log_datadog_events": false,
    "tengu_marble_anvil": false,
    "tengu_tool_pear": false,
    "tengu_scarf_coffee": false,
    "tengu_keybinding_customization_release": false,
    "tengu_penguins_enabled": true,
    "tengu_thinkback": false,
    "tengu_chomp_inflection": true,
    "tengu_oboe": false,
    "tengu_silver_lantern": false,
    "tengu_birthday_hat": false,
    "tengu-top-of-feed-tip": {
      "tip": "",
      "color": "dim"
    },
    "tengu_code_diff_cli": false,
    "tengu_snippet_save": false,
    "tengu_pr_status_cli": false,
    "tengu_mcp_elicitation": false,
    "tengu_post_compact_survey": false,
    "tengu_copper_lantern": false,
    "tengu_claudeai_mcp_connectors": false,
    "tengu_plan_mode_interview_phase": false,
    "tengu_tst_names_in_messages": false,
    "tengu_fgts": false,
    "enhanced_telemetry_beta": false,
    "tengu_vinteuil_phrase": false,
    "tengu_tool_search_unsupported_models": null,
    "tengu_system_prompt_global_cache": false,
    "tengu_attribution_header": true,
    "tengu_cache_plum_violet": false,
    "tengu_streaming_tool_execution2": false,
    "tengu_immediate_model_command": false,
    "tengu_marble_lantern_disabled": false,
    "tengu_session_memory": false
  },
  "hasCompletedOnboarding": true,
  "firstStartTime": "2026-02-18T08:19:28.999Z",
  "userID": "b75a05e999749987175c1526579e0e74b7b4b20e3233e61059437f18e573178a",
  "opusProMigrationComplete": true,
  "sonnet1m45MigrationComplete": true,
  "clientDataCache": {
    "data": null,
    "timestamp": 1771415254074
  },
  "cachedChromeExtensionInstalled": false,
  "changelogLastFetched": 1771414844414,
  "lastReleaseNotesSeen": "2.1.45",
  "officialMarketplaceAutoInstallAttempted": true,
  "officialMarketplaceAutoInstalled": true
}
```

## 命令行启动注意问题

```shell
claude --model Pro/zai-org/GLM-5
```

## 其他同类产品

- https://openai.com/zh-Hans-CN/codex/
- https://opencode.ai/zh
- gemini cli
- Kimi K2 Cli
- Qwen Code
- Rovo Dev CLI
