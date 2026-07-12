# llm-line (Draft)

**llm-line** is a line-oriented output format and reference specification for stabilizing large LLM outputs. It is designed to avoid common JSON syntax failures (missing commas, unclosed braces) when asking LLMs to emit many flat fields, and to enable robust streaming parsing and schema validation.

**Status:** Draft — Feedback welcome

---

## Key goals
- **Resilience** Prevent syntax breakage common with JSON in large flat payloads.  
- **Streaming friendly** Line-oriented design for low-memory, high-throughput parsing.  
- **Practicality** Provide a reference, regex-free Java streaming parser implementation.  
- **Extensibility** Support schema validation, unknown-tag passthrough, and DTO mapping.

---

## Quick start
1. Read **SPEC.md** for the format definition.  
2. Try the sample in `examples/sample1.llm-line`.  
3. Open an Issue to discuss bugs, edge cases, or adoption questions.

**Note** This project is a draft. We welcome constructive feedback and real-world use cases. If you think JSON is sufficient for your workflow, please share that perspective — comparisons and bridge strategies are especially valuable.

---

## Example
```text
@RECORD user
id: 123
name: John Doe

@LIST roles
- admin
- editor

@TEXT bio
John Doe is a sample user.
```

---

## Contributing and Security (short)
- **Contributing**: This repo is in draft. Please open an Issue to start design discussions. For code changes, fork and submit a Pull Request referencing the related Issue. Small doc fixes may be submitted directly as PRs.  
- **Security**: If you believe you found a security issue, please report it privately rather than opening a public Issue.

---

## License
MIT

---
