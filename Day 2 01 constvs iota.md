## **5. Constant vs. `iota` Summary**
| Feature | `const` | `iota` |
|---------|--------|--------|
| Type | Immutable fixed values | Auto-incrementing constants |
| Default Behavior | Must assign manually | Starts at `0`, increments per line |
| Scope | Works for all types | Primarily for integers |
| Flexibility | Fixed values only | Allows auto-numbering, bit-shifting |
| Best Use Case | Pi value, fixed settings | Enums, bit flags, custom sequences |
