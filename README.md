# Windows Driver Vulnerability Research

I build automated systems to find security vulnerabilities in Windows kernel drivers at scale.

## Research Focus

```
Driver Binary
     │
     ▼
┌─────────────────────────────────────────────────────┐
│            Automated Analysis Pipeline              │
├─────────────────────────────────────────────────────┤
│  Decompilation → Function Matching → Semantic Diff  │
│       ↓               ↓                  ↓          │
│    Ghidra         Hash-LCS         Rule Engine      │
└─────────────────────────────────────────────────────┘
     │
     ▼
Security-Relevant Patches (ranked by exploitability)
```

**Problem**: Vendors release hundreds of driver updates yearly. Most are cosmetic. A few silently fix critical vulnerabilities without CVEs.

**Solution**: Automated semantic analysis that surfaces the 2% of changes that matter—use-after-free fixes, missing bounds checks, user/kernel boundary hardening.

## Tools

| Project | Purpose |
|---------|---------|
| [**AutoPiff**](https://github.com/splintersfury/AutoPiff) | Semantic patch diffing engine with YAML rules for detecting vulnerability fixes |
| [**driver_analyzer**](https://github.com/splintersfury/driver_analyzer) | Scalable pipeline: Karton + MWDB + Ghidra for batch driver analysis |

## Methodology

1. **Collect** — Ingest driver binaries from multiple sources into MWDB
2. **Pair** — Automatically match driver versions by product metadata
3. **Decompile** — Headless Ghidra produces normalized C pseudocode
4. **Diff** — Semantic rules detect security-relevant patterns in changes
5. **Rank** — Scoring model prioritizes findings by exploitability
6. **Report** — Actionable output for manual exploitation research

## Detection Capabilities

- **Lifetime fixes**: Null-after-free patterns (`ExFreePool` → `ptr = NULL`)
- **Bounds checks**: Length validation before `memcpy`/`RtlCopyMemory`
- **User boundary**: Added `ProbeForRead`/`ProbeForWrite` calls
- **Integer overflow**: Safe math helpers (`RtlULongAdd`, `RtlSizeTMult`)
- **State hardening**: Interlocked reference counting

## Tech Stack

**Analysis**: Python, Ghidra (headless), YAML rule engine  
**Infrastructure**: Karton, MWDB Core, Redis, MinIO, Docker  
**Reverse Engineering**: IDA Pro, Ghidra, WinDbg, x64dbg

---

**Blog**: [threatunpacked.com](https://threatunpacked.com)

<sub>Turning 4-12 hours of manual patch review into 2-5 minutes of automated triage.</sub>
