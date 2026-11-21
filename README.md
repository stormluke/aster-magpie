# Astra Magpie

AsterGate 的 Magpie Provider。登录后自动读取该 Key 可用的 Claude 模型，并在 Magpie 的 Usage 和 Provider 账号卡中显示 5 小时、每周额度及重置时间。额度按 AsterGate 报告的上游账号分开显示，供查看。

## 安装与使用

在插件目录执行：

    magpie plugin add "$PWD"
    magpie plugin login astergate
    magpie quota astergate

也可在 Magpie 的 Plugins 页面添加本目录，再从 AsterGate 的 Provider 卡登录。登录时使用交互式提示输入 AsterGate API Key。模型以 astergate/ 为前缀，可在 Magpie 的模型选择器中手动选用。

## 接口

- 余额页先通过 POST /usage-session/challenge 和 POST /usage-session/login 建立会话，再用 Cookie 调用 GET /usage-session/report。Key 随 JWE 提交，登录会话只在内存中使用。
- 模型发现使用 GET /v1/models。推理经 POST /v1/messages 转发，保留 Anthropic 的请求格式和流式响应。
- 两组接口均使用 https://beta.astergate.cc。额度报告没有可读数据时，Magpie 显示查询状态。

[Magpie 插件接口](https://usemagpie.ai/docs/plugins) · [AsterGate 余额页](https://beta.astergate.cc/key-usage)

## 验证

在插件目录执行 node --test，覆盖 JWE 请求、额度映射、模型发现和 Messages 转发。已使用 Magpie 0.1.4479 和本地模拟上游验证登录、额度卡以及 SSE 响应。
