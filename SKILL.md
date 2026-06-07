---
name: skill-search
description: Search GitHub for Claude Code skills by name. Use when user types "+" followed by a skill name (e.g., "+ brainstorming"), or says "search skill X", "find skill X on GitHub", "有没有X这个skill".
---

# Skill Search — GitHub Skills 搜索器

## 触发方式

用户输入 `+ <名称>` 就触发。

## 工作流

1. 收到触发后，用 `WebSearch` 工具搜 GitHub：
   - 搜索词：`<name> claude code skill github`
   - 如果没结果，追加：`<name> skill npx install`

2. 同时用 Bash 调 GitHub API（走代理）获取精确结果：
   ```bash
   curl -s --proxy http://127.0.0.1:7897 \
     "https://api.github.com/search/repositories?q=<name>+skill+claude+code&sort=stars&per_page=10" \
     -H "Accept: application/vnd.github+json" \
     -H "User-Agent: skill-search"
   ```

3. 合并两路结果，用表格呈现：
   ```
   | ⭐ | 仓库 | 描述 | 安装 |
   |----|------|------|------|
   | 3547 | XBuilderLAB/cheat-on-content | ... | npx skills add ... |
   ```

4. 如果用户确认要装某个，直接执行安装。

## 规则

- 按 Stars 排序，最多展示 8 个
- 每个带安装命令：`npx skills add <repo>`
- 不展示无关仓库（如 Android ROM、游戏脚本等）
