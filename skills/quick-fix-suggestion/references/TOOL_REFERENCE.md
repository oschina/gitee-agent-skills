# Tool Reference Guide

## Tool Availability

| Tool | Availability | Notes |
|------|--------------|-------|
| `glob` | 100% | Built-in, always works |
| `read` | 100% | Built-in, always works |
| `grep` | 100% | Built-in Agent tool (not system grep) |
| `ast_grep_search` | 100% | Built-in, no external CLI needed |
| `lsp_find_references` | 100% | Built-in LSP integration |
| `bash` (git) | ~90% | Try-catch required |

## Tool Selection Guide

### Use `glob` when:
- You know the file name pattern (e.g., `auth`, `user`, `controller`)
- You want to explore directory structure
- You need to find all files of a certain type

### Use `grep` (built-in) when:
- You need to search file contents
- You have specific strings to search (error messages, function names)
- You need regex pattern matching
- **This is the Agent's grep tool, NOT the system grep command**

### Use `ast_grep_search` when:
- You need semantic understanding (find all function definitions)
- You want to match code structure, not just text
- You need to find patterns that are hard to express with regex

### Use `lsp_find_references` when:
- You need accurate reference finding (not text-based)
- You're analyzing a specific symbol (function, class, variable)
- You want to understand the call graph

## Common Search Patterns

### Find Function Definitions

**JavaScript/TypeScript:**
```typescript
grep({
  pattern: `function\\s+${funcName}`,
  path: repoPath,
  include: "*.{js,ts,jsx,tsx}",
  output_mode: "content"
})
```

**Python:**
```typescript
grep({
  pattern: `def\\s+${funcName}`,
  path: repoPath,
  include: "*.py",
  output_mode: "content"
})
```

**Go:**
```typescript
grep({
  pattern: `func\\s+.*${funcName}`,
  path: repoPath,
  include: "*.go",
  output_mode: "content"
})
```

### Find Error Messages
```typescript
grep({
  pattern: "Error message here",
  path: repoPath,
  output_mode: "content",
  head_limit: 20
})
```

### Find API Endpoints
```typescript
grep({
  pattern: `router\\.(get|post|put|delete)\\s*\\(['"]`,
  path: repoPath,
  include: "*.{js,ts}",
  output_mode: "content"
})
```

### Find Configuration Files
```typescript
glob('**/{config,configs,settings}/**/*.{js,json,yaml,yml}', { cwd: repoPath })
glob('**/.env*', { cwd: repoPath })
```

### AST-Based Semantic Search
```typescript
ast_grep_search({
  pattern: "function $NAME($$$) { $$$ }",
  lang: "javascript",
  paths: [repoPath]
})

ast_grep_search({
  pattern: "class $NAME { $$$ }",
  lang: "typescript",
  paths: [repoPath]
})
```

### Git History Analysis (with Fallback)
```typescript
try {
  const gitOutput = await bash({
    command: `cd ${repoPath} && git log --since="3 days ago" --name-only --pretty=format:`,
    description: "Get recently modified files"
  });
} catch (error) {
  // Git not available - skip
}
```

## Performance Best Practices

1. **Limit Search Scope**: Use `include` parameter to limit file types
2. **Combine Tools**: Use glob first to narrow scope, then grep
3. **Use `head_limit`**: Stop after N matches to save time
4. **Cache Results**: Within session, avoid duplicate searches
5. **Prefer AST**: For complex patterns, ast_grep_search is more accurate

## When to Use Gitee API as Fallback

Only use remote search when:
1. Local clone is incomplete (other branches needed)
2. Repository is extremely large
3. Rate limit: Maximum 3 remote searches per invocation
