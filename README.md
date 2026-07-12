# llm-line (Draft)

**llm-line** is a line-oriented output format and reference spec for stabilizing large LLM outputs. It is designed to avoid common JSON syntax failures (missing commas, unclosed braces) when asking LLMs to emit many flat fields, and to enable robust streaming parsing and schema validation.

**Status:** Draft / Feedback welcome

---

## Key goals
- **Resilience:** Prevent syntax breakage common with JSON in large flat payloads.  
- **Streaming friendly:** Line-oriented design for low-memory, high-throughput parsing.  
- **Practicality:** Provide a reference, regex-free Java streaming parser implementation.  
- **Extensibility:** Support schema validation, unknown-tag passthrough, and DTO mapping.

---

## Quick start
1. Read **SPEC.md** for the format definition.  
2. Try the sample in `examples/sample1.llm-line`.  
3. Open an Issue for bugs, edge cases, or adoption questions.

---

## Example (human-readable)
```
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

## Why use llm-line
When you ask an LLM to output thousands of flat fields, tiny formatting slips can break JSON and halt batch pipelines. **llm-line** removes bracketed nesting and uses explicit record/list/text markers so LLM outputs remain machine-parseable even when the model produces minor deviations.

---

## Parser & Implementation
A **regex-free Java streaming parser** is provided as a reference implementation to demonstrate production-grade performance and deterministic behavior. The parser is designed to:
- Process input line-by-line with constant memory overhead.
- Emit events for records, lists, and text blocks.
- Integrate with schema validators and mappers.

(Implementation code will be available in `parser/` or linked repo.)

---

## Contributing
See **CONTRIBUTING.md**. Short guidelines:
- Open an Issue for design questions or edge cases.
- PRs should include tests or sample inputs demonstrating the change.
- Keep changes backward-compatible or document breaking changes in `CHANGELOG.md`.

---

## License
MIT

---

## Contact / Security
See **SECURITY.md** for vulnerability reporting. For general questions, open an Issue.
