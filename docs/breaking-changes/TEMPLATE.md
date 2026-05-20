# Breaking Changes: [package-name] [from-version] → [to-version]

## Overview

Brief description of what changed and why this migration is necessary.

---

## Breaking Changes

### [BC-1] [Short description of change]

**Old API:**
```js
// How it was used before
const old = require('package').method(arg1, arg2)
```

**New API:**
```js
// How it should be used now
const { newMethod } = require('package')
newMethod({ option1: arg1, option2: arg2 })
```

**Ripgrep pattern to find usages:**
```
rg "oldMethod\(" --type js
rg "require\('package'\)" --type js
```

**Test scenarios to validate migration:**
- [ ] Basic usage with default options returns expected result
- [ ] Error handling works correctly with new API
- [ ] Edge case: [describe edge case]

---

### [BC-2] [Short description of change]

**Old API:**
```js
```

**New API:**
```js
```

**Ripgrep pattern to find usages:**
```
rg "" --type js
```

**Test scenarios to validate migration:**
- [ ] 

---

## Notes

Any additional context Claude should know:
- Which usages are safe to ignore
- Patterns that look like usages but are not (to reduce false positives)
- Known edge cases in this codebase
- Test framework and runner being used (jest, mocha, vitest, etc.)
