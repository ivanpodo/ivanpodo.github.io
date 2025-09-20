---
title: Common Tasks
layout: page
nav_order: 4
---

# Common Tasks

This page contains useful snippets and examples for common documentation tasks using Markdown and the Just-the-Docs theme.

## Code Blocks

### Basic Code Block
Display code with syntax highlighting:

```c
void initialize_system(void) {
  // Configure hardware
  setup_gpio();
  setup_uart();
  
  // Initialize variables
  uint8_t status = 0;
  g_system_ready = true;
}
```

**Markdown:**
````markdown
```c
void initialize_system(void) {
  // Configure hardware
  setup_gpio();
  setup_uart();
  
  // Initialize variables
  uint8_t status = 0;
  g_system_ready = true;
}
```
````

### Inline Code
Use backticks for inline code: `variable_name` or `function_call()`

**Markdown:**
```markdown
Use backticks for inline code: `variable_name` or `function_call()`
```

## Buttons and Links

### Basic Button
[Link button](https://just-the-docs.com){: .btn }

```markdown
[Link button](https://just-the-docs.com){: .btn }
```

### Button Variations
[Primary button](https://example.com){: .btn .btn-primary }
[Purple button](https://example.com){: .btn .btn-purple }
[Blue button](https://example.com){: .btn .btn-blue }
[Green button](https://example.com){: .btn .btn-green }

```markdown
[Primary button](https://example.com){: .btn .btn-primary }
[Purple button](https://example.com){: .btn .btn-purple }
[Blue button](https://example.com){: .btn .btn-blue }
[Green button](https://example.com){: .btn .btn-green }
```

## Callouts and Alerts

### Information Callout
{: .info }
> This is an informational message providing helpful context.

```markdown
{: .info }
> This is an informational message providing helpful context.
```

### Warning Callout
{: .warning }
> This is a warning message about potential issues.

```markdown
{: .warning }
> This is a warning message about potential issues.
```

### Good/Success Callout
{: .good }
> This indicates successful completion or best practices.

```markdown
{: .good }
> This indicates successful completion or best practices.
```

### Tips Callout
{: .tips }
> This provides helpful tips and recommendations.

```markdown
{: .tips }
> This provides helpful tips and recommendations.
```

## Tables

### Basic Table

| Function | Parameters | Return Value |
|----------|------------|--------------|
| `init_uart()` | None | `STATUS_OK` or `STATUS_ERROR` |
| `send_byte()` | `uint8_t data` | `bool` success |
| `read_buffer()` | `uint8_t *buf, size_t len` | `size_t` bytes_read |

**Markdown:**
```markdown
| Function | Parameters | Return Value |
|----------|------------|--------------|
| `init_uart()` | None | `STATUS_OK` or `STATUS_ERROR` |
| `send_byte()` | `uint8_t data` | `bool` success |
| `read_buffer()` | `uint8_t *buf, size_t len` | `size_t` bytes_read |
```

### Aligned Table

| Left Aligned | Center Aligned | Right Aligned |
|:-------------|:--------------:|--------------:|
| Text         | Text           | 123           |
| More text    | Centered       | 456.78        |

**Markdown:**
```markdown
| Left Aligned | Center Aligned | Right Aligned |
|:-------------|:--------------:|--------------:|
| Text         | Text           | 123           |
| More text    | Centered       | 456.78        |
```

## Lists

### Ordered List
1. First step
2. Second step
   1. Sub-step 2.1
   2. Sub-step 2.2
3. Third step

### Unordered List
- Main point
- Another point
  - Sub-point
  - Another sub-point
    - Deep nested point
- Final point

### Task List
- [x] Completed task
- [x] Another completed task
- [ ] Pending task
- [ ] Future task

**Markdown:**
```markdown
- [x] Completed task
- [ ] Pending task
```

## Text Formatting

### Emphasis
- **Bold text** using `**text**`
- *Italic text* using `*text*`
- ***Bold and italic*** using `***text***`
- ~~Strikethrough~~ using `~~text~~`

### Blockquotes
> This is a blockquote.
> 
> It can span multiple lines.
>> Nested blockquotes are also possible.

**Markdown:**
```markdown
> This is a blockquote.
> 
> It can span multiple lines.
>> Nested blockquotes are also possible.
```

## Images

### Basic Image
```markdown
![Alt text](/assets/images/image.png)
```

### Image with Link
```markdown
[![Alt text](/assets/images/image.png)](https://example.com)
```

### Image with Caption
```markdown
![Alt text](/assets/images/image.png)
*Figure 1: Image caption*
```

## Horizontal Rule

Use three or more hyphens, asterisks, or underscores:

---

```markdown
---
```

## Collapsible Sections

<details>
<summary>Click to expand</summary>

This content is hidden by default and can be expanded by clicking the summary.

```c
void hidden_code_example(void) {
  // This code is inside a collapsible section
}
```

</details>

**Markdown:**
```markdown
<details>
<summary>Click to expand</summary>

Content goes here...

</details>
```

## Linking to Sections

### Internal Links
Link to a section on the same page: [Jump to Tables](#tables)

Link to another page: [C Coding Standards](guidelines/c-coding-standards.html)

Link to a specific section on another page: [MISRA Guidelines](guidelines/c-coding-standards.html#key-misra-c-guidelines)

**Markdown:**
```markdown
[Jump to Tables](#tables)
[C Coding Standards](guidelines/c-coding-standards.html)
[MISRA Guidelines](guidelines/c-coding-standards.html#key-misra-c-guidelines)
```

## File Downloads

Provide downloadable files:

[Download Configuration](/assets/tools/.clang-format){: .btn .btn-primary }

```markdown
[Download Configuration](/assets/tools/.clang-format){: .btn .btn-primary }
```

## Math Expressions (if MathJax is enabled)

Inline math: $a^2 + b^2 = c^2$

Block math:
$$
\sum_{i=1}^{n} i = \frac{n(n+1)}{2}
$$

**Markdown:**
```markdown
Inline math: $a^2 + b^2 = c^2$

Block math:
$$
\sum_{i=1}^{n} i = \frac{n(n+1)}{2}
$$
```

## Keyboard Keys

Use `<kbd>` tags to show keyboard keys:

Press <kbd>Ctrl</kbd> + <kbd>C</kbd> to copy

```markdown
Press <kbd>Ctrl</kbd> + <kbd>C</kbd> to copy
```

## Footnotes

Here's a sentence with a footnote[^1].

[^1]: This is the footnote content.

**Markdown:**
```markdown
Here's a sentence with a footnote[^1].

[^1]: This is the footnote content.
```