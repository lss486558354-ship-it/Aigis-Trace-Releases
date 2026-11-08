# Aigis Trace Releases

本仓库只存放 **Aigis** 的内测安装包、校验文件与发布说明，**不包含源码**。源码仓库保持私有。

- 最新安装包：请打开 [Releases](https://github.com/lss486558354-ship-it/Aigis-Trace-Releases/releases/latest)
- 内测激活：<https://aigis.uno/beta>
- 产品与安全说明：<https://aigis.uno/>
- 隐私说明：<https://aigis.uno/privacy>

## 下载校验

每个 Release 同时提供安装包和 `.sha256` 校验文件。安装包未配置代码签名，Windows SmartScreen 可能显示“未知发布者”；下载后请核对 SHA-256。

```powershell
Get-FileHash .\Aigis-1.9.4-setup.exe -Algorithm SHA256
```

## 说明

授权与机器绑定仅用于内测发放管理，不是安全边界。Aigis 采用基于人类在环（Human-in-the-loop）的强边界管控与审计回滚机制，不等同于操作系统权限控制。

由 Aigis Trace 出品。
