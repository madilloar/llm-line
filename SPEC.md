# llm-line Core Specification (Draft)

**Version:** 0.1.0-draft  
**Purpose:** Define a line-oriented format for reliable LLM output ingestion and streaming parsing.

---

## 1. Overview
llm-line is a minimal, human-readable, line-oriented format that represents records, lists, and free text blocks using explicit markers. It intentionally avoids nested bracketed syntax (e.g., `{}` or `[]`) to reduce the chance of syntax errors from LLM output.

---

## 2. Basic Concepts
- **Line**: The atomic unit. Each logical element is represented by one or more lines.
- **Record**: A named collection of key:value pairs introduced by `@RECORD <name>`.
- **List**: A named list introduced by `@LIST <name>`; items are prefixed with `- `.
- **Text block**: A named free-text block introduced by `@TEXT <name>`; content lines follow until a blank line or next marker.
- **Marker lines**: Lines starting with `@` denote the start of a new element.

---

## 3. Syntax
- **Record start**
  ```
  @RECORD <name>
  key1: value1
  key2: value2
  ```
  - Keys are ASCII identifiers (letters, digits, `_`, `-`).
  - Values are raw text until end-of-line. No quoting required; newlines are not allowed in values (use TEXT for multi-line).

- **List start**
  ```
  @LIST <name>
  - item1
  - item2
  ```
  - List items are single-line entries prefixed by `- `.

- **Text block**
  ```
  @TEXT <name>
  This is a multi-line
  free-text block.
  ```
  - Text block ends at the first blank line or next `@` marker.

- **Comments and blank lines**
  - Blank lines separate top-level elements.
  - Lines beginning with `#` are comments and ignored by the parser.

---

## 4. Escaping and Special Characters
- Values are raw; if you need to include a leading `- ` or `@` in a value, prefix with a backslash `\`.
- Backslash escaping only applies to the first character when it would otherwise be interpreted as a marker.

Examples:
```
@RECORD note
text: \@not-a-marker
item: \- not a list item
```

---

## 5. Schema and Validation
- **Schema** is optional but recommended for production. A schema maps record/list/text names to expected fields, types, and constraints.
- Validation occurs after parsing: the parser emits a neutral event stream; a separate validator applies schema rules and reports WARN/ERROR.
- **Unknown fields**: By default, unknown keys are preserved and passed through; validators can be configured to reject or coerce.

Schema example (JSON Schema-like pseudocode):
```json
{
  "user": {
    "type": "record",
    "fields": {
      "id": {"type": "integer"},
      "name": {"type": "string"}
    }
  }
}
```

---

## 6. Streaming Parser Behavior (reference)
- The parser reads input line-by-line and emits events:
  - `start_record(name)`, `field(name, value)`, `end_record()`
  - `start_list(name)`, `list_item(value)`, `end_list()`
  - `start_text(name)`, `text_line(value)`, `end_text()`
- The parser never buffers the entire document; memory usage is proportional to the largest single element (e.g., a single text block line).

---

## 7. Error Handling and Robustness
- **Lenient parsing**: The parser tolerates minor deviations (extra whitespace, missing blank line separators) and emits WARN events rather than failing where possible.
- **Strict mode**: For CI or ingestion pipelines, enable strict mode to convert WARNs into ERRORS.
- **Recovery**: On encountering an unparseable line, the parser attempts to resynchronize at the next `@` marker.

---

## 8. Interoperability
- **JSON bridge**: Converters can map records/lists/text to JSON objects/arrays/strings. Example mapping:
  - `@RECORD user` → `{ "user": { "id": 123, "name": "John" } }`
  - `@LIST roles` → `{ "roles": ["admin","editor"] }`
- **Markdown bridge**: Text blocks can be mapped to Markdown sections.

---

## 9. Versioning and Compatibility
- Use semantic versioning for the spec (MAJOR.MINOR.PATCH).  
- Add a `@SPEC-VERSION x.y.z` marker in files when necessary for compatibility checks.

---

## 10. Examples
See `examples/` for sample files. Minimal example:
```
@RECORD user
id: 1
name: Alice

@LIST tags
- beta
- test

@TEXT note
This is a note about Alice.
```

---

## 11. FAQ (short)
- **Q:** Why not fix JSON?  
  **A:** JSON is strict; LLMs frequently produce small deviations. A line-oriented format reduces the blast radius of such deviations.
- **Q:** Is this a replacement for JSON?  
  **A:** No. llm-line is a pragmatic ingestion format; converters to/from JSON are supported.

---

## 12. Feedback
This is a draft. Please open Issues for bugs, edge cases, or adoption stories.
