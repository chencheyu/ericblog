---
date: 2024-06-26
publish: true
slug: 48bb57d6-f1e1-4508-b798-1bea1cb67465
tags:
- CS
title: Markdown Parse Survey On Js and Python
---
### 目標

取得一份 markdown 的 ast，在遍歷樹結構時同時修改節點值，不會增刪節點，最後在轉換回 markdown
具體專案需求請見
最基本要支援 common markdown，註腳可選

### 結論

只有 [mistune](https://mistune.lepture.com/en/latest/index.html) 可以做到

```python
astrender = MarkdownRenderer()
# plugins=['strikethrough', 'footnotes', 'table','task_lists' , 'math']
ast = mistune.create_markdown(renderer='ast', escape=False)(md)
mdc = astrender(ast, state=mistune.core.BlockState())
```

文件沒有明寫此用法但 v3.0.2 確定可以在專案中使用
plugins 被註解應為加入 plugins 時無法 `astrender` 回 markdown，需要自己撰寫 render

### Survey

背景調查過以下函式庫
