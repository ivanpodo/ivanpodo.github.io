---
layout: default
title: Development Tools
parent: Guidelines
nav_order: 2
---

# Development Tools for C

This section provides configuration files and usage instructions for automated code quality tools specifically for C programming (.c and .h files).

## Code Formatting and Analysis Tools

### Clang-Format

Clang-format automatically formats C code according to our style guidelines, ensuring consistent code formatting across the entire codebase.

**Configuration File:** [Download .clang-format](/assets/tools/.clang-format)

#### Features
- Enforces 2-space indentation
- Maintains 100-character line limit
- Applies consistent brace placement
- Aligns declarations and comments
- Sorts includes automatically

#### Installation
```bash
# Ubuntu/Debian
sudo apt-get install clang-format

# macOS
brew install clang-format

# Fedora
sudo dnf install clang-tools-extra
```

#### Usage
```bash
# Format a single file in-place
clang-format -i file.c

# Format all C/C++ files in a directory
find . -name "*.c" -o -name "*.h" | xargs clang-format -i

# Check formatting without modifying files
clang-format --dry-run --Werror file.c

# Show formatting diff
clang-format file.c | diff file.c -
```

---

### Clang-Tidy

Clang-tidy performs static analysis, identifies bugs, and enforces coding standards including MISRA-C guidelines and NASA Power of Ten rules.

**Configuration File:** [Download .clang-tidy](/assets/tools/.clang-tidy)

#### Features
- Enforces variable naming conventions (3+ characters, no underscore prefix)
- Checks for uninitialized variables
- Limits function complexity (max 100 lines)
- Detects common bugs and security issues
- Suggests modern C practices
- Enforces MISRA-C and NASA guidelines

#### Installation
```bash
# Ubuntu/Debian
sudo apt-get install clang-tidy

# macOS
brew install llvm

# Fedora
sudo dnf install clang-tools-extra
```

#### Usage
```bash
# Run clang-tidy on a single file
clang-tidy file.c -- -I/path/to/includes

# Run with automatic fixes
clang-tidy -fix file.c -- -I/path/to/includes

# Run on entire project with compilation database
clang-tidy -p build/ src/*.c

# Generate compilation database with CMake
cmake -DCMAKE_EXPORT_COMPILE_COMMANDS=ON .

# Generate compilation database with Bear
bear -- make
```

---

## Integration in Projects

### Project Setup

1. **Copy configuration files to your project root:**
```bash
wget https://ivanpodo.github.io/assets/tools/.clang-format
wget https://ivanpodo.github.io/assets/tools/.clang-tidy
```

2. **Add to version control:**
```bash
git add .clang-format .clang-tidy
git commit -m "Add code formatting and analysis tools configuration"
```

### IDE Integration

#### Visual Studio Code
Install extensions:
- **C/C++** by Microsoft
- **Clang-Format** by xaver

Settings (`.vscode/settings.json`):
```json
{
  "editor.formatOnSave": true,
  "C_Cpp.formatting": "clangFormat",
  "C_Cpp.clang_format_style": "file",
  "clang-tidy.checks": ["file"]
}
```

#### CLion
- Go to **Settings → Editor → Code Style**
- Enable **ClangFormat**
- Set to use `.clang-format` file from project

#### Vim
Add to `.vimrc`:
```vim
" Format on save
autocmd BufWritePre *.c,*.h :ClangFormat

" Manual formatting
map <C-K> :ClangFormat<cr>
```

### CI/CD Integration

#### GitHub Actions
```yaml
name: Code Quality

on: [push, pull_request]

jobs:
  format-check:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    
    - name: Install tools
      run: |
        sudo apt-get update
        sudo apt-get install -y clang-format clang-tidy
    
    - name: Check formatting
      run: |
        find . -name "*.c" -o -name "*.h" | xargs clang-format --dry-run --Werror
    
    - name: Run static analysis
      run: |
        find . -name "*.c" | xargs clang-tidy --warnings-as-errors='*'
```

#### Pre-commit Hook
Create `.git/hooks/pre-commit`:
```bash
#!/bin/sh
# Check C files formatting and analysis before commit

# Get staged C files
files=$(git diff --cached --name-only --diff-filter=ACM | grep -E '\.(c|h)$')

if [ -n "$files" ]; then
  # Check formatting
  echo "Checking code formatting..."
  for file in $files; do
    clang-format --dry-run --Werror "$file" || {
      echo "Formatting issues found. Run: clang-format -i $file"
      exit 1
    }
  done
  
  # Run static analysis
  echo "Running static analysis..."
  for file in $files; do
    if [[ "$file" == *.c ]]; then
      clang-tidy "$file" -- || {
        echo "Static analysis issues found in $file"
        exit 1
      }
    fi
  done
fi

echo "Code quality checks passed!"
```

Make it executable:
```bash
chmod +x .git/hooks/pre-commit
```

### Makefile Integration
```makefile
# Format all source files
format:
	@find . -name "*.c" -o -name "*.h" | xargs clang-format -i
	@echo "Code formatted successfully"

# Check formatting without changes
format-check:
	@find . -name "*.c" -o -name "*.h" | xargs clang-format --dry-run --Werror

# Run static analysis
lint:
	@clang-tidy src/*.c -- -I./include

# Clean, format, and analyze
quality: format lint
	@echo "Code quality checks complete"
```

---

## Best Practices

1. **Run tools before committing** - Use pre-commit hooks to ensure code quality
2. **Configure your editor** - Enable format-on-save for immediate feedback
3. **Include in CI/CD** - Catch issues early in the development pipeline
4. **Review tool suggestions** - Not all automatic fixes should be applied blindly
5. **Keep configurations updated** - Regularly review and update tool configurations
6. **Document exceptions** - If you must disable a check, document why with comments

---

## Troubleshooting

### Common Issues

**Issue:** Clang-format not found
```bash
# Check if installed
which clang-format

# Install if missing (see Installation section above)
```

**Issue:** Different formatting between developers
```bash
# Ensure everyone uses the same version
clang-format --version

# Specify version in .clang-format
# BasedOnStyle: LLVM-14
```

**Issue:** Clang-tidy too slow
```bash
# Use compilation database for faster analysis
cmake -DCMAKE_EXPORT_COMPILE_COMMANDS=ON .

# Run on changed files only
git diff --name-only | grep '\.c$' | xargs clang-tidy
```

**Issue:** False positives from clang-tidy
```c
// Disable specific check for a line
int magic = 42; // NOLINT(readability-magic-numbers)

// Disable for entire block
// NOLINTBEGIN(readability-magic-numbers)
#define BUFFER_SIZE 1024
#define MAX_RETRIES 3
// NOLINTEND(readability-magic-numbers)
```