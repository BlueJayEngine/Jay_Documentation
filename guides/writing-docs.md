---
title: Writing Documentation
order: 1
category: guides
description: Tips for writing great documentation
---

# Writing Documentation

A guide to writing clear, useful documentation with JayDocs.

## Markdown Basics

JayDocs supports full **GitHub Flavored Markdown** (GFM):

### Text Formatting

- **Bold** with `**text**`
- *Italic* with `*text*`
- ~~Strikethrough~~ with `~~text~~`
- `Inline code` with backticks

### Links & Images

```markdown
[Link text](https://example.com)
![Alt text](./image.png)
```
[Test_link](/index) 
### Lists

Ordered:
1. First item
2. Second item
3. Third item

Unordered:
- Item one
- Item two
- Item three

### Task Lists

- [x] Write the docs
- [x] Add syntax highlighting
- [ ] Take over the world

## Code Blocks

Use fenced code blocks with a language identifier:

````markdown
```javascript
const greeting = 'Hello, JayDocs!';
console.log(greeting);
```
````

Which renders as:

```javascript
const greeting = 'Hello, JayDocs!';
console.log(greeting);
```

### Supported Languages

Syntax highlighting works for all major languages:

```python
def fibonacci(n):
    """Generate fibonacci sequence up to n."""
    a, b = 0, 1
    while a < n:
        yield a
        a, b = b, a + b
```

```rust
fn main() {
    let numbers: Vec<i32> = (1..=10).collect();
    let sum: i32 = numbers.iter().sum();
    println!("Sum: {}", sum);
}
```

### Jai Example

```jai
#import "Basic";

main :: () {
    print("Hello, JayDocs!\n");
    
    numbers := [1, 2, 3, 4, 5];
    for numbers {
        print("Number: %\n", it);
    }
}
```

## Tables

```markdown
| Column 1 | Column 2 | Column 3 |
| -------- | -------- | -------- |
| Cell 1   | Cell 2   | Cell 3   |
```

| Feature          | Status | Notes              |
| ---------------- | ------ | ------------------ |
| Markdown         | ✅     | Full GFM support   |
| Syntax Highlight | ✅     | All major languages|
| Dark Mode        | ✅     | Auto or manual     |
| Search           | ✅     | Fuzzy full-text    |

## Blockquotes

> **Tip:** Use blockquotes to highlight important information.

> **Warning:** This is a warning message that draws attention.

## Organizing Your Docs

### Folder Structure

Group related docs in folders — they become sidebar categories:

```
docs/
├── index.md           # Home
├── getting-started.md # Top-level page
├── api/               # "api" category
│   ├── overview.md
│   └── endpoints.md
└── guides/            # "guides" category
    ├── writing-docs.md
    └── deployment.md
```

### Page Ordering

Use the `order` frontmatter to control sort order within categories:

```yaml
---
title: First Page
order: 1
---
```

Pages without an `order` default to `999` (appear last).
