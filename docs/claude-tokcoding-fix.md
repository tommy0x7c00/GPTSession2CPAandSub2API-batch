# Claude Code + TokCoding 修复教程

## 现象

Claude Code 可以正常启动，但偶尔显示：

```text
Insufficient credits, task paused...
```

同时余额充足，普通请求可以成功。

## 根因

Claude Code 2.1.210 的后台摘要/小请求在未指定小模型时，可能使用旧模型：

```text
claude-3-5-sonnet-20241022
```

TokCoding 不支持该模型，实际返回 `404 model_not_found`，网关随后被 Claude Code 显示成了“Insufficient credits”。这不是账户余额不足。

## CC-Switch 设置

打开 CC-Switch，进入当前 Claude Provider 的环境变量，添加或确认以下两项：

```text
CLAUDE_CODE_DISABLE_EXPERIMENTAL_BETAS=1
ANTHROPIC_SMALL_FAST_MODEL=claude-haiku-4-5
```

同时确认主模型配置仍指向 TokCoding 支持的模型，例如：

```text
ANTHROPIC_MODEL=claude-opus-4-8
ANTHROPIC_DEFAULT_OPUS_MODEL=claude-opus-4-8
ANTHROPIC_DEFAULT_SONNET_MODEL=claude-opus-4-8
ANTHROPIC_DEFAULT_HAIKU_MODEL=claude-haiku-4-5
```

不要把 API Token 写入教程、日志或提交到 Git。

## 重启并验证

1. 保存 CC-Switch 配置并重启 CC-Switch。
2. 退出已经运行的 Claude Code，再重新启动。旧进程不会自动读取新环境变量。
3. 在项目目录执行：

```bash
claude -p 'reply exactly OK' --model claude-opus-4-8 --output-format json --no-session-persistence
```

返回 `"result":"OK"` 即表示主请求正常；输出中的 `modelUsage` 应能看到 `claude-haiku-4-5`，说明后台小请求也已使用受支持的模型。

## 结论

`CLAUDE_CODE_DISABLE_EXPERIMENTAL_BETAS=1` 只用于规避新版请求中的 400 参数兼容问题；要解决 `Insufficient credits`，还必须设置 `ANTHROPIC_SMALL_FAST_MODEL`。
