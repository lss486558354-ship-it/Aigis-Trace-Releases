# Security Policy / 安全报告

## 支持范围

当前仅支持 **Aigis v1.9.5 内测版（Windows x64）**。更早版本不再单独修复。

## 如何报告

- 可能被利用的越权、数据泄露、token 绕过、审批绕过：请**先私信** GitHub 账号
  [`@lss486558354-ship-it`](https://github.com/lss486558354-ship-it)，不要公开
  可直接利用的 0day。
- 可公开讨论的行为差异、文档错误、安装问题：请在
  [Releases](https://github.com/lss486558354-ship-it/Aigis-Trace-Releases/releases)
  仓库开 issue，并附版本、Claude Code 版本、复现步骤和脱敏日志。
- 报告请注明属于 L3 UI、L4 真实模型、L5 对抗、L6 故障还是 L7 安装问题。
  自动化测试通过不等于真实模型或真实攻击通过。

请勿提交：真实他人的项目数据、API Key、token、设备码、确认短语、密码或
签名私钥。复现所需数据请自行构造。

## 测试边界

允许在本机、自己的项目、自己的测试账号上做安全研究。禁止：

- DoS / 资源耗尽攻击；
- 撞库、钓鱼、社会工程；
- 供应链投毒；
- 读取、修改或泄露他人真实项目与凭证；
- 攻击 Anthropic 或其他第三方服务。

## 已知边界（不是新漏洞）

- 应用层隔离，不是 OS 沙盒，不承诺抵抗内核或同权限恶意进程。
- 来源项目共享开关是跨进程只读 SQLite，无法用 `FOR UPDATE` 强事务；
  最终复检之后完成的关闭属于线性化边界。
- 消费者项目总开关在进程内同锁 + generation 下同步。
- 安装包未签名，不启用自动更新。
- Claude Code < 2.1.295 无法设置 `onFailure: block`，Aigis 在该版本为
  `degraded`；如果 Aigis 自身 hook 启动失败，可能按非阻断处理。
- 全局第三方 hook 报错不属于 Aigis 注入 hook，但 Aigis 会继承全局设置。

## 源码状态

Aigis 源码仓库目前为 private，且未附 LICENSE。本仓库只存放安装包、校验文件
与发布说明；本次是公开内测与白帽挑战，不称“开源项目”。
