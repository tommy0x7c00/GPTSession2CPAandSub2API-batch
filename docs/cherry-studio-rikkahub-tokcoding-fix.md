# Cherry Studio / RikkaHub + TokCoding 配置教程

本文适用于通过 TokCoding 调用 Claude 模型。API Key 只填写在客户端本地，不要写入文档或提交到 Git。

## 共同参数

使用 TokCoding 实际支持的模型 ID，例如：

```text
claude-opus-4-8
claude-haiku-4-5
```

`CLAUDE_CODE_DISABLE_EXPERIMENTAL_BETAS` 和 `ANTHROPIC_SMALL_FAST_MODEL` 是 Claude Code 专用环境变量，Cherry Studio 与 RikkaHub 不需要配置这两个变量。

## Cherry Studio

1. 打开「设置」→「模型服务」→「Anthropic」；没有现成 Provider 时，新建一个 Anthropic Provider。
2. API Host 填：

   ```text
   https://tokcoding.cc
   ```

   Cherry Studio 的 Anthropic 适配器会自动补成 `https://tokcoding.cc/v1`。如果版本界面要求填写完整地址，也可以直接填 `https://tokcoding.cc/v1`，不要重复填写 `/v1`。
3. 填入 TokCoding API Key。
4. 添加上面的模型 ID，并将要使用的模型设为默认。
5. 保存后用「测试连接」或发送一条短消息验证。

如果出现 `400` 或 `Extra inputs are not permitted`：

- 确认模型使用的是上面的实际 ID，而不是 `claude-3-5-sonnet-20241022`；
- 关闭模型的高级思考/实验参数后重试；
- 关闭 Prompt Cache 后重试。TokCoding 支持后再重新开启。

## RikkaHub

根据 RikkaHub 官方配置结构：

1. 打开「设置」→「供应商」→「添加供应商」。
2. 类型选择 **Claude**。
3. Base URL 必须填完整的 Anthropic API 根路径：

   ```text
   https://tokcoding.cc/v1
   ```

   RikkaHub 会在此地址后追加 `/messages`，所以不要填 `https://tokcoding.cc`，也不要填完整的 `/v1/messages`。
4. 填入 API Key，保存供应商。
5. 在模型页添加并选择 `claude-opus-4-8` 或 `claude-haiku-4-5`。
6. 首次测试建议关闭「Prompt Caching」。如果 TokCoding 返回 thinking、cache_control 或参数不支持，再关闭模型的 Reasoning/思考选项；普通文本请求确认成功后再逐项开启。

## 最小验证

先使用 Haiku 发送短消息，确认连通后再测试 Opus 和长上下文。不要用旧模型 `claude-3-5-sonnet-20241022` 或不受支持的 Sonnet 4.6，TokCoding 对它们返回模型不可用错误，部分客户端会将该错误误显示为额度不足。

## 官方实现依据

- [Cherry Studio Anthropic Provider](https://github.com/CherryHQ/cherry-studio/blob/main/packages/provider-registry/src/providers/anthropic.ts)
- [Cherry Studio API Host 格式化](https://github.com/CherryHQ/cherry-studio/blob/main/src/shared/utils/api/format.ts)
- [RikkaHub ProviderSetting](https://github.com/rikkahub/rikkahub/blob/master/ai/src/main/java/me/rerere/ai/provider/ProviderSetting.kt)
- [RikkaHub ClaudeProvider](https://github.com/rikkahub/rikkahub/blob/master/ai/src/main/java/me/rerere/ai/provider/providers/ClaudeProvider.kt)
