---
name: search-repos
description: Search for open source repositories on Gitee with advanced filtering by language, popularity, and activity. Use when the user wants to find repositories, explore open source projects, discover libraries by language, or compare multiple projects without forking. Keywords: search repos, find repositories, open source discovery, explore projects, compare libraries.
license: MIT
compatibility: Requires Gitee MCP Server configured with access to search_open_source_repositories tool
metadata:
  author: gitee-agent-skills
  version: "1.0.0"
  related_skills: repo-explorer, search-and-fork
---

# Search Repositories on Gitee

Search for open source repositories with advanced filters. After finding interesting projects, optionally explore them in depth.

## Prerequisites

- Gitee MCP Server configured
- Required tool: `search_open_source_repositories`
- Optional: `repo-explorer` skill for deep dives

## When to Use This Skill

Use `search-repos` when the user wants to:
- Find repositories by language (Go, Python, Java, etc.)
- Discover projects by keywords or categories
- Compare multiple repositories
- Explore without immediate forking intent

**Not for:** Forking repositories (use `search-and-fork` instead)

## Supported Filters

| Parameter | Type | Description | Example |
|-----------|------|-------------|---------|
| `q` | string | **Required.** Search keywords | "web framework", "ORM" |
| `language` | string | Filter by programming language | "Go", "Python", "JavaScript" |
| `sort` | string | Sort field: `stars_count`, `forks_count`, `watches_count`, `created_at`, `last_push_at` | `stars_count` |
| `order` | string | Sort direction: `desc` (default), `asc` | `desc` |
| `owner` | string | Filter by namespace/organization | "oschina", "alibaba" |
| `fork` | boolean | Include forked repositories | `false` (default) |

## Workflow

### Step 1: Gather Requirements

Ask the user:
1. **Keywords** - What functionality? (e.g., "web framework", "database ORM")
2. **Language** - Programming language preference? (Go / Python / Java / JavaScript / TypeScript / Rust / etc.)
3. **Sort by** - Popularity (`stars_count`) or recent activity (`last_push_at`)?
4. **Filters** - Include forks? Specific organization?

### Step 2: Build Search Queries

Try multiple keyword variations for better coverage:
- English: "web framework"
- Chinese: "web 框架"
- Related terms: "HTTP router", "REST API framework"

### Step 3: Execute Search

Call `search_open_source_repositories` with parameters:

```javascript
{
  q: "web framework",
  language: "Go",           // If specified
  sort: "stars_count",      // or "last_push_at"
  order: "desc",
  per_page: 20
}
```

### Step 4: Present Results

Format output as:

```
## 🔍 Search Results: [keywords] ([language])

Found N repositories (sorted by [criteria]):

| # | Repository | ⭐ Stars | 🍴 Forks | Language | Updated |
|---|------------|---------|----------|----------|---------|
| 1 | owner/repo | 1.2k | 150 | Go | 2 days ago |
| 2 | owner/repo | 890 | 98 | Go | 1 week ago |
| 3 | owner/repo | 650 | 75 | Go | 3 weeks ago |

---

### 📋 Detailed Information

**#1: [Full Repository Name]**
- **URL**: https://gitee.com/owner/repo
- **Description**: [Full description from API]
- **License**: [license type if available]
- **Tags**: [topics if available]

**#2: [Full Repository Name]**
[Same format...]
```

### Step 5: Offer Next Actions

After presenting results:

```
What would you like to do next?

1. 🔍 **Refine search** - Try different keywords/filters
2. 🔎 **Deep dive** - Explore a specific repository (requires repo-explorer skill)
3. 📊 **Compare** - View side-by-side comparison
4. ✅ **Done** - End the session
```

**If user chooses "Deep dive":**
- Ask which repository (by number or name)
- Check if `repo-explorer` skill is available
- If yes: "I'll use the repo-explorer skill to analyze [owner/repo]"
- If no: Use `get_file_content` to manually read README and key files

### Step 6: Iterative Refinement

If results are unsatisfactory, adjust:
- Broader or more specific keywords
- Different language filter
- Change sort criteria
- Toggle fork inclusion

## Example Conversations

**Example 1: Language-specific search**
```
User: Find me Python machine learning libraries

→ search_open_source_repositories(
  q="machine learning",
  language="Python",
  sort="stars_count"
)
```

**Example 2: Active projects**
```
User: Show me recently updated Go web frameworks

→ search_open_source_repositories(
  q="web framework",
  language="Go",
  sort="last_push_at"
)
```

**Example 3: Discovery without specific goal**
```
User: What are popular JavaScript UI libraries?

→ search_open_source_repositories(
  q="UI component",
  language="JavaScript",
  sort="stars_count"
)

→ User: Can you explore the first one?
→ Check for repo-explorer skill and use it
```

## Comparison with Related Skills

| Task | Use This | Instead Of |
|------|----------|------------|
| Search without fork intent | `search-repos` ✅ | `search-and-fork` |
| Search + Fork in one flow | `search-and-fork` ✅ | `search-repos` |
| Deep exploration after search | Both can lead to `repo-explorer` | - |

## Notes

- Results limited to public repositories on Gitee
- Language names use GitHub conventions (e.g., "Go", not "Golang")
- Keyword variations yield different results - experiment with synonyms
- Consider both English and Chinese keywords for broader coverage
- The `repo-explorer` skill provides detailed analysis; this skill focuses on discovery
