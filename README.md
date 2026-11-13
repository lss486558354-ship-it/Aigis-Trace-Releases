# Aigis Trace Releases

本仓库只存放 **Aigis** 的内测安装包、校验文件与发布说明，**不包含源码**。源码仓库保持私有。

- 最新安装包：请打开 [Releases](https://github.com/lss486558354-ship-it/Aigis-Trace-Releases/releases/latest)
- 内测激活：<https://aigis.uno/beta>
- 产品与安全说明：<https://aigis.uno/>
- 隐私说明：<https://aigis.uno/privacy>

## 下载校验

每个 Release 同时提供安装包和 `.sha256` 校验文件。安装包未配置代码签名，Windows SmartScreen 可能显示“未知发布者”；下载后请核对 SHA-256。

```powershell
Get-FileHash .\Aigis-1.9.5-setup.exe -Algorithm SHA256
```

## 白帽挑战

当前版本是公开内测，**不是“已经防住黑客”的成品**，也不是 OS 沙盒。L0/L1 自动化通过，L2 部分契约通过；L3 UI 人工流、L4 真实模型、L5 完整安全对抗尚未执行。

- 挑战范围：跨项目记忆只读共享、逐条批准、一次性授权、token 隔离、路径边界、审批疲劳、来源撤销/开关竞态、审计绕过、MCP/工具边界。
- 已知边界：应用层隔离，不是 OS 沙盒；来源项目共享开关是跨进程只读 SQLite，最终复检之后完成的关闭属于线性化边界，不宣称强事务；安装包未签名。
- 报告方式：可能被利用的越权或数据泄露，请先私信 `@lss486558354-ship-it`，不要公开可直接利用的 0day。可公开讨论的行为差异请开 issue。不接受 DoS、撞库、供应链投毒或读取他人真实项目数据。
- 源码状态：Aigis 源码仓库目前为 private，且未附 LICENSE；本次是公开内测与白帽挑战，不称“开源项目”。

## 说明

授权与机器绑定仅用于内测发放管理，不是安全边界。Aigis 采用基于人类在环（Human-in-the-loop）的强边界管控与审计回滚机制，不等同于操作系统权限控制。

由 Aigis Trace 出品。
