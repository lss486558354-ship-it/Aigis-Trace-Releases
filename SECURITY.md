# Aigis Security Policy

**Last updated:** 2026-11-14
**Applies to:** Aigis v1.9.5 public beta and later versions until this policy is replaced.
**Product:** Windows 10/11 x64 desktop workbench for Claude Code.
**Source status:** private repository; no LICENSE; **not** an open-source project. The source is not part of this policy.

> 中文摘要：本政策适用于 Aigis v1.9.5 公开内测（Windows x64）的黑盒安全研究。善意研究在满足“只用自己数据、最小化访问、私下报告、不敲诈”等条件时适用安全港；禁止 DoS/压测、撞库、供应链投毒、社工/物理攻击、读取他人真实数据、公开可直接利用的 0day。响应目标：3 个工作日内确认、7 个工作日内初判；S0/S1 30 天、High 60 天、Medium/Low 90 天修复目标（不是保证）。L3–L5 未跑；同用户恶意进程不在威胁模型内；SHA-256 只是完整性校验，不是签名。以下英文为完整条款。

> 本政策是安全研究授权与协调流程说明，不构成法律意见，也不构成对违法行为的授权。发布前请由熟悉适用法域的律师审阅。若本政策的任何表述与适用法律冲突，以适用法律为准。

---

## 1. Scope

We welcome good-faith, black-box security research against **public release artifacts** of Aigis, including:

- the installer and update artifacts;
- the local daemon's HTTP/IPC surface;
- Gate, path policy, and protected-path enforcement;
- cross-project memory read-only sharing (triple switch, per-item approval, one-time grant);
- approval UI, audit trail, and session/turn boundaries;
- broker/daemon token handling and local IPC authentication;
- MCP/tool configuration boundaries;
- installer and environment interactions (Claude Code version, Git Bash, sidecar startup).

**Out of scope:**

- Anthropic services, Claude Code itself, or any third-party service;
- denial-of-service, stress/load testing, or resource-exhaustion attacks;
- credential stuffing, password guessing, or attacks on third-party accounts;
- social engineering, physical access, or supply-chain attacks;
- accessing, modifying, deleting, or exfiltrating other users' real project data;
- testing on shared/production systems without explicit written authorization;
- the known boundaries listed in section 5, unless you can show the implementation violates the stated contract.

---

## 2. Safe Harbor

To the extent permitted by applicable law, we will not initiate or support civil or criminal action against you for security research that:

1. is conducted in good faith and strictly within this policy's scope;
2. uses only your own test projects, test accounts, and test data;
3. minimizes access to data, stops immediately if you encounter another person's real data, and does not copy, store, or disclose it;
4. does not modify, delete, or persist access to data beyond what is necessary to demonstrate the issue;
5. does not degrade, overload, or disrupt the service or other users;
6. does not violate privacy, confidentiality, or applicable law;
7. reports the issue privately and gives us a reasonable opportunity to fix it before public disclosure;
8. does not demand payment, threaten disclosure, or engage in extortion.

This safe harbor:

- does **not** authorize any illegal act, nor does it bind third parties (including Anthropic or other service providers);
- does **not** cover research that is out of scope, malicious, reckless, or in violation of this policy;
- is **not** a promise of a specific legal outcome; it is our stated intent as the project maintainer;
- may be revised; the version in effect is the one published at the canonical URL at the time of your research.

If you are unsure whether a test is in scope, ask first via the private channel in section 3. We would rather answer a question than have you guess.

---

## 3. How to Report

**Preferred:** GitHub private vulnerability reporting:

https://github.com/lss486558354-ship-it/Aigis-Trace-Releases/security/advisories/new

**Fallback:** contact `@lss486558354-ship-it` on GitHub. In the first message, do **not** include exploit details; ask for a secure channel. We will respond with an encrypted/private route.

Please include:

- Aigis version and build (e.g., v1.9.5, SHA-256 of installer);
- Windows version, Claude Code version, and relevant environment details;
- clear reproduction steps and the observed vs. expected behavior;
- impact assessment and the security invariant you believe is violated;
- redacted logs/screenshots (no API keys, tokens, passwords, or third-party personal data);
- whether you want public credit, anonymity, or no credit.

**Do not open a public issue containing exploit details or a working 0day.** Public behavior differences that are not exploitable can be discussed in an issue.

**Encryption:** we do not currently publish a long-term PGP key. If you need encrypted transport, ask in the first private message and we will provide a key or an alternative secure channel. Do not send secrets in plaintext.

---

## 4. Response Targets

These are targets, not contractual guarantees. Complex issues may take longer; we will communicate status rather than go silent.

| Stage | Target |
| --- | --- |
| Acknowledge receipt | 3 business days |
| Initial triage / severity | 7 business days |
| Critical (S0/S1: cross-project data exposure, auth bypass, arbitrary code execution) | fix target 30 days; status update every 7 days |
| High | fix target 60 days |
| Medium / Low | fix target 90 days |
| Coordinated disclosure | default 90 days after report, or earlier by mutual agreement |

We may ask for more information, a minimal reproduction, or a retest. If a fix is not feasible within the target, we will explain why and agree on a new timeline.

**Credit:** we offer optional public credit in release notes / a hall of fame. We do **not** currently offer a monetary bounty. Do not assume a payout; ask before doing bounty-style work.

---

## 5. Known Boundaries (Not New 0-days)

These are documented limitations of v1.9.5. Reporting one of them as a new vulnerability is not eligible unless you can show the implementation violates the stated contract.

1. **Write lease / Full Trust is a tool-call authorization, not a process-runtime sandbox.** Once a turn permits Bash/AI execution, project scripts and developer commands (`python`, `node`, `npm`, `pytest`, etc.) run with the current Windows user's privileges. Aigis's path policy, control-plane protection, and shadow snapshots do not constrain what those child processes read, write, connect to, or modify. The script may be generated by the model itself, not only an external trojan. This is a boundary against direct model file edits, not a same-machine trojan defense or an AI-code sandbox. Same-user malicious processes remain outside the threat model.
2. **Approval modal shows metadata, not content.** The human sees the title, source, and scope, not the memory body or `content_sha256`; title/source display names can be influenced by the source side. This is an informed-consent and social-engineering risk. Default-deny focus, Esc-to-deny, and the 3-pending-per-session cap reduce fatigue and misclicks; they are not content review and do not make the system "absolutely safe."
3. **Application-layer isolation, not an OS sandbox.** Aigis does not claim to resist kernel-level attacks, admin-level attackers, debugger/injection, or a malicious process running as the same user. Same-user malicious processes are outside the threat model.
4. **Source-project switch is cross-process read-only SQLite.** There is no `FOR UPDATE` cross-process transaction. The precise linearization contract is:
   - the read's linearization point is the final commit-time read of the source switch/epoch;
   - if that final read occurs **before** the source close transaction commits, the read may return after the close, but it is linearized **before** the close;
   - if the final check occurs **after** the close commits, it must observe the new value and fail closed.
   We do not claim a cross-process strong transaction. A report that treats the first case as a new 0-day is out of scope; a report showing the second case succeeding is in scope.
5. **Consumer-project master switch is in-process locked + generation-based.** After the close call returns, no new read may commit. This is the stronger of the two switch guarantees.
6. **Claude Code < 2.1.295 is `degraded`.** `onFailure: block` is unavailable; if Aigis's own hook fails to start, older versions may treat it as non-blocking. Upgrade to 2.1.295 or later.
7. **Global third-party hook noise.** The `cjs/loader:1433` error comes from a third-party global hook (missing `plugin-hook-bootstrap.js`), not from an Aigis-injected hook. Aigis inherits global Claude settings, so the noise can appear in Aigis sessions. Cleaning the global hook or upgrading Claude Code does not change Aigis's gate contract.
8. **Installer is unsigned.** SHA-256 is an **integrity check**, not a signature and not proof of publisher identity. Windows may show "Unknown publisher". Automatic updates are not enabled.
9. **Snapshot is not streaming.** Limits: 2,000,000 bytes per file, 64,000,000 bytes total, 500 changed files, 45-second Git timeout; files containing NUL are not loaded. Over-limit results are unavailable/truncated; failures fail closed. There is no 2MB/NUL-specific test suite yet.
10. **Path tests have one skip; hardlinks are not covered.** `tests/test_workspace_boundary.py` has 21 cases: 20 pass, 1 skipped because the host does not allow creating symlinks. Junction passes; UNC read/write denial passes; traversal/glob/trailing-dot denial passes. The path policy resolves existing symlinks/junctions; the skipped symlink case requires L5 validation on Windows. NTFS hardlinks are not reparse points and are not resolved by the current path policy; whether a same-volume, user-writable file outside the project can be referenced by an in-project hardlink requires L5 validation. This is not claimed as a vulnerability before a PoC. This is an application-layer path boundary, not a physical/OS sandbox.
11. **L3–L5 are NOT RUN for v1.9.5.** L0/L1 automation passes; L2 is partial. Automated tests do not prove real UI, real-model, or adversarial security.
12. **Source is private and has no LICENSE.** Aigis is not an open-source project. Do not describe this release as "open source".
13. **Planned v2.0 hardening is not delivered in v1.9.5.** This includes single-writer state persistence, authenticated IPC, independent lock files/`BEGIN IMMEDIATE`, revision/epoch rollback protection, global approval caps, and the Memory Draft Layer. These are plans, not current capabilities.

---

## 6. Research Guidelines

- Use a dedicated test project and test data. Do not test on your primary work project.
- Keep a backup of any state you intend to manipulate.
- If you find a way to read another user's data, stop, do not save the content, and report immediately.
- Do not run automated scanners that degrade service; do not run DoS or load tests.
- Do not bypass rate limits with multiple accounts or rotating identities.
- Do not test `aigis.uno` production beyond non-invasive requests; no stress testing.
- If you believe a DoS is possible, describe the hypothesis and a minimal safe reproduction; do not actually exhaust resources.
- Do not attempt to access, modify, or delete data outside your own test project.
- If a test could affect other users, stop and ask first.

---

## 7. Coordinated Disclosure

We prefer coordinated disclosure. We will:

- acknowledge and triage your report;
- keep you informed of remediation progress;
- credit you publicly if you want;
- coordinate a disclosure date with you.

We ask that you:

- do not publish a working exploit before a fix or a mutually agreed date;
- do not disclose other users' data;
- do not use the issue for marketing or extortion.

If we cannot agree on a timeline, we will explain the reason and propose a new date. If a vulnerability is being actively exploited, we may disclose earlier with reporter coordination.

---

## 8. Changes to This Policy

This policy may be updated as the product and threat model evolve. The "Last updated" date and canonical URL are authoritative. A later version supersedes earlier versions.

---

## 9. Contact

- **Private report:** https://github.com/lss486558354-ship-it/Aigis-Trace-Releases/security/advisories/new
- **GitHub:** https://github.com/lss486558354-ship-it
- **Website:** https://aigis.uno/
- **security.txt:** https://aigis.uno/.well-known/security.txt

No phone support. For non-security product issues, use the public release repository issues.

---

## 10. Acknowledgments

We thank researchers who report issues in good faith. Credit is opt-in; let us know how you want to be named (or if you prefer to remain anonymous).
