# Astra Magpie

<img src="https://beta.astergate.cc/favicon.svg" alt="AsterGate" width="72" height="72">

AsterGate 的 Magpie Provider。登录后自动读取该 Key 可用的 Claude 模型，并在 Magpie 的 Usage 和 Provider 账号卡中显示 5 小时、每周额度及重置时间。额度按 AsterGate 报告的上游账号分开显示，供查看。

## 安装与使用

在 Magpie 的终端执行：

    magpie plugin add github:stormluke/aster-magpie#v0.2.1
    magpie plugin login astergate
    magpie quota astergate

登录时在交互式提示输入 API Key。模型以 astergate/ 为前缀。已有账号升级后继续使用原来的 Key。

## 配置

- 默认使用 https://beta.astergate.cc。自托管且协议兼容的 AsterGate 可以在 **Magpie 配置目录**创建 astergate.json，内容为 `{"origin":"https://gateway.example.com"}`。只接受 HTTPS 源地址，模型、推理和额度均使用这个地址。API Key 会发给该地址，请使用你信任的网关。保存后在 Magpie 中关闭再开启该插件以重新加载。
- Provider 的「显示模型专属额度」默认开启。关闭后仅显示通用额度和 Key 预算。下一次额度查询生效。
- 额度查询在内存中短暂复用登录会话，拒绝该会话时会重新登录一次。

## 接口

- 余额页先通过 POST /usage-session/challenge 和 POST /usage-session/login 建立会话，再用 Cookie 调用 GET /usage-session/report。Key 随 JWE 提交，登录会话只在内存中使用。
- 模型发现使用 GET /v1/models。推理经 POST /v1/messages 转发，保留 Anthropic 的请求格式和流式响应。
- 两组接口使用同一个网关。额度报告没有可读数据时，Magpie 显示查询状态。

[Magpie 插件接口](https://usemagpie.ai/docs/plugins) · [AsterGate 余额页](https://beta.astergate.cc/key-usage)

## 验证

在插件目录执行 node --test，覆盖 JWE 请求、额度映射、模型发现和 Messages 转发。已使用 Magpie 0.1.4479 和本地模拟上游验证登录、额度卡以及 SSE 响应。
