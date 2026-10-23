# Level 2 - Fallout 漏洞复习笔记

## 1. 漏洞本质 (Logic Flaw)
* **类型**：构造函数命名失误 / 未授权访问
* **原因**：开发者原本想写构造函数，但将函数名错拼为 `Fal1out()`（将字母 `l` 误写为数字 `1`）。
* **后果**：该函数变成了任何外部账户都可以随意调用的**普通公开函数**，导致 `owner` 权限可被任意人夺取。

## 2. EVM 底层物理流转 (State Machine Diagram)
```text
[黑客/攻击合约] 
      │
      │ 1. 发送 Calldata: 0x6fab5fac (Fal1out 的 4 字节选择器)
      ▼
[Fallout 目标合约]
      │
      ├──> EVM 读取头 4 字节 0x6fab5fac，精准跳转至 Fal1out() 指令块
      ├──> 执行物理指令: SSTORE(Slot 0, msg.sender)
      └──> 物理存储槽 Slot 0 原 owner 地址被彻底擦除，覆写为【黑客地址】
