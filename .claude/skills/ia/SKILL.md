---
name: ia
description: Guide for querying IBM i Impact Analysis (iA) repository tables. Use when analyzing program dependencies, tracing field impact, mapping call hierarchies, finding where objects are used, reading source code, or exploring IBM i codebases. ALWAYS use this skill for ANY IBM i analysis question — it contains optimized tool flows that answer most questions in 1-2 calls instead of 5+.
---

# iA Impact Analysis — Agent Guide

iA by [programmers.io](https://programmers.io/ia/) pre-parses IBM i source code (RPG, CL, COBOL, DDS) into 35+ Db2 repository tables. All tables live in the `${IA_LIBRARY}` schema.

## Efficiency-First Approach

**Goal: Answer user questions in 1-2 tool calls, not 5+.**

Most iA questions can be answered with a single well-chosen tool. Before calling any tool, consult [references/query-flows.md](references/query-flows.md) for the optimal tool sequence.

### Quick Tool Selection

| User Intent | Single Best Tool | Returns |
|-------------|------------------|---------|
| "What uses X?" | `ia_where_used` | All referencing objects |
| "Impact of field change?" | `ia_field_impact` | Affected programs + usage type |
| "LFs/views over file X?" | `ia_file_dependencies` | All dependent logical files, indexes, views |
| "Tell me about program X" | `ia_program_detail` (section=*ALL) | Calls, files, subs, vars, overrides — ALL in one query |
| "Call tree for X?" | `ia_call_hierarchy` | Callers + callees |
| "Dead code?" | `ia_unused_objects` | Zero-reference objects |
| "Complexity hotspots?" | `ia_code_complexity` (member=*ALL) | All programs ranked by complexity |
| "Find object named X?" | `ia_object_lookup` | Type/library/attr (supports % wildcards) |
| "Repository overview?" | `ia_dashboard` | Full inventory stats |

### When to Chain (and When NOT to)

**DO chain** when you see `*SRVPGM` in results — service programs are amplifiers; check what binds to them.

**DO chain for field impact:** Logical files and views built over a physical file **propagate field changes** to all programs using those LFs. When analyzing field changes, always ask the user if they want the full blast radius including LF-dependent programs:
1. `ia_field_impact` → direct references to the PF
2. `ia_file_dependencies` → LFs/views over the PF
3. `ia_where_used` on each LF → programs using those LFs

**DON'T chain** for:
- Simple counts (just count your results)
- Every `*PGM` in results (overkill — only investigate critical ones)
- `ia_source_code` before token analysis (go straight to `ia_rpg_source_tokens`)

## Tool Preference Rule

**Prefer dedicated `ia_*` MCP tools over `execute_sql`.** The repo ships 27 purpose-built tools that are parameter-validated, bounded, and tested. **Must only fall back** to `execute_sql` when no dedicated tool fits (then consult [references/sql-patterns.md](references/sql-patterns.md)).

## Dedicated Tools (27)

### Discovery — start here
| Tool | Purpose |
|------|---------|
| `ia_library_files` | List every file/table in the iA repository library |
| `ia_object_lookup` | Resolve an object name → type, library, attribute (wildcard with `%`) |
| `ia_object_list` | Inventory objects by type (`*PGM`, `*SRVPGM`, `*FILE`, ...) |
| `ia_program_info` | Program/module metadata: source file, member, attribs, last change |
| `ia_dashboard` | Repo health summary: categories, line counts, library map |
| `ia_repo_config` | iA repository configuration settings |

### Where-used & references — highest-impact queries
| Tool | Purpose |
|------|---------|
| `ia_where_used` | Broad where-used: all objects referencing `object_name` (optional type filter) |
| `ia_where_used_detail` | Where-used with library precision + `source_exist` flag (joins IAOBJMAP) |
| `ia_reference_count` | Lightweight: counts of references grouped by type |
| `ia_field_impact` | Field-level blast radius: programs affected if field X in file Y changes |

### Call graph
| Tool | Purpose |
|------|---------|
| `ia_call_hierarchy` | Callers / callees / both for a program or module |
| `ia_call_parameters` | Parameters passed at each external call site |
| `ia_circular_deps` | Detect two-way circular call chains |

### Program internals
| Tool | Purpose |
|------|---------|
| `ia_program_variables` | All variables declared in a member (type, length, DS flag) |
| `ia_data_structures` | Data structure definitions and subfields |
| `ia_subroutines` | BEGSR/EXSR with usage counts (dead-subroutine detection) |

### Files & overrides
| Tool | Purpose |
|------|---------|
| `ia_file_fields` | Field-level metadata for a database file (richer than DSPFFD) |
| `ia_file_dependencies` | Logical files, indexes, views dependent on a physical file (IADSPDBR) |
| `ia_file_overrides` | OVRDBF statements — real file routing vs. declared F-spec |
| `ia_override_chain` | Chained OVRDBF dependencies (A→B→C) |

### Source-level analysis
| Tool | Purpose |
|------|---------|
| `ia_rpg_source_tokens` | Token-level RPG parse (IAPGMREF) |
| `ia_cl_source_tokens` | Token-level CL parse (IACPGMREF) |

### Lifecycle, complexity, cleanup
| Tool | Purpose |
|------|---------|
| `ia_object_lifecycle` | Creation/change/last-used dates, days-used count |
| `ia_code_complexity` | IF/DO/SQL/GOTO/PROC counts, executable lines, call stats |
| `ia_unused_objects` | Dead code candidates (never referenced) |
| `ia_dds_to_ddl_status` | DDS→DDL modernization tracking |
| `ia_exception_log` | iA parser errors per member |

### Fallback
| Tool | Purpose |
|------|---------|
| `execute_sql` | Raw SELECT for anything none of the above covers |
| `describe_sql_object` | DDL/column details for a table, view, index, or procedure |

## Start Here — Decision Tree

| User Intent | Preferred Tool | Fallback SQL Pattern |
|-------------|----------------|----------------------|
| What references object X? | `ia_where_used` (or `ia_where_used_detail` for library precision) | [#1](references/sql-patterns.md) |
| How many refs to X? | `ia_reference_count` | — |
| What does X call / who calls X? | `ia_call_hierarchy` | [#2, #3](references/sql-patterns.md) |
| Params passed at each call site? | `ia_call_parameters` | — |
| Impact of changing field F in file X? | `ia_field_impact` | [#4](references/sql-patterns.md) |
| Variables in program X? | `ia_program_variables` | [#5](references/sql-patterns.md) |
| Data structures in program X? | `ia_data_structures` | — |
| Subroutines in program X? | `ia_subroutines` | — |
| File fields / formats for file X? | `ia_file_fields` | — |
| File overrides (OVRDBF)? | `ia_file_overrides`, `ia_override_chain` | — |
| What type/library is object X? | `ia_object_lookup` | [#6](references/sql-patterns.md) |
| Inventory of objects by type? | `ia_object_list` | — |
| Program metadata / compile info? | `ia_program_info` | [#14](references/sql-patterns.md) |
| Lifecycle / last-used dates? | `ia_object_lifecycle` | — |
| Dead code candidates? | `ia_unused_objects` | [#15](references/sql-patterns.md) |
| Complexity hotspots? | `ia_code_complexity` | — |
| Circular call chains? | `ia_circular_deps` | — |
| Repo health / member inventory? | `ia_dashboard` | — |
| List tables in iA library? | `ia_library_files` | [#7](references/sql-patterns.md) |
| Raw RPG/CL token stream? | `ia_rpg_source_tokens`, `ia_cl_source_tokens` | — |
| Source code for member X? | `execute_sql` on IAQRPGSRC / IAQCLSRC / IAQDDSSRC | [#16, #17](references/sql-patterns.md) |
| Logical files over physical file X? | `ia_file_dependencies` | — |
| Copybook impact? | `execute_sql` on COPYBOOK_MEMBER_DETAIL | [#11](references/sql-patterns.md) |
| Service program exports? | `execute_sql` on IMPORTED_EXPORTED_PROCEDURE_DETAILS | [#12](references/sql-patterns.md) |
| Anything else | Find the table in [tables.md](references/tables.md), schema via `describe_sql_object`, query via `execute_sql` |

## Parameter Rules for Dedicated Tools

- Object/member names are **10-char uppercase** — pass `'CUSTMAST'`, not `'custmast'`
- `object_type` parameters use star-prefixed form: `*PGM`, `*SRVPGM`, `*FILE`, `*DSPF`, `*CMD`, `*MODULE`, `*DTAARA`
- Wildcard `*ALL` means "no filter" — use it as the default for optional type filters
- All tools cap results with `limit` (default 200–500) — bump it explicitly if you hit the cap
- `ia_object_lookup` is the only tool supporting `%` wildcards in the name

## SQL Rules (for `execute_sql` fallback)

- Always qualify tables: `IADEMODEV.TABLE_NAME`
- Always limit results: `FETCH FIRST N ROWS ONLY`
- **SELECT only** — never INSERT/UPDATE/DELETE
- Object names are **10-char uppercase**
- **Always must run** `describe_sql_object` first if unsure of column names — never guess

## Interpreting Results

- **`*SRVPGM`** in results = amplifier — always check what depends on it next (chain `ia_where_used` or `ia_reference_count`)
- **`*DSPF`** = user-facing screen impact — flag prominently
- **Empty results** are suspicious — object may be invoked by job scheduler or external system
- Always: **summarize by object type**, count references, state risk level, suggest a concrete next step

## References

- **Optimal tool flows for common queries** → [references/query-flows.md](references/query-flows.md) (READ FIRST)
- **Analysis playbooks & chaining strategy** → [references/playbook.md](references/playbook.md)
- **SQL templates** for `execute_sql` fallbacks → [references/sql-patterns.md](references/sql-patterns.md)
- **All 35+ iA tables** catalog → [references/tables.md](references/tables.md)

---

## Self-Improvement Protocol

When you encounter limitations or inefficiencies with the iA tools, you have permission and are encouraged to improve them:

### When to Improve

1. **Tool returns wrong columns** — Fix the SQL in `impact-analysis.yaml`
2. **Common query requires 3+ tool calls** — Create a new combined tool or update existing one
3. **User asks question no tool answers** — Add a new tool to `impact-analysis.yaml`
4. **Tool description is misleading** — Update the description in yaml
5. **Skill guidance is incomplete** — Update this skill or its reference files

### How to Improve Tools (impact-analysis.yaml)

1. **Diagnose** — Use `describe_sql_object` to verify correct column names
2. **Edit** — Modify `impact-analysis.yaml` following these conventions:
   - Tool key: `ia_snake_case`
   - MCP name: `ia-kebab-case`
   - Use `:param_name` for bound parameters
   - Use `${IA_LIBRARY}` for schema (never hardcode)
   - Always include `FETCH FIRST :limit ROWS ONLY`
   - SQL must be read-only (SELECT only)
3. **Restart** — Tell user to restart MCP server: "Restart the MCP server to apply the fix"
4. **Verify** — Test the fixed tool

### How to Improve This Skill

Edit files in `.claude/skills/ia/`:
- `SKILL.md` — Main guidance (keep under 150 lines)
- `references/query-flows.md` — Optimal tool chains
- `references/playbook.md` — Analysis strategies
- `references/sql-patterns.md` — Fallback SQL templates
- `references/tables.md` — Table catalog

### Improvement Principles

1. **Reduce tool calls** — If users commonly need A then B then C, consider a combined tool
2. **Make the common case fast** — 80% of queries should need only 1 tool call
3. **Document patterns** — When you discover an efficient approach, add it to query-flows.md
4. **Fix forward** — Don't work around broken tools; fix them

### Example: Adding a New Combined Tool

If users frequently ask "show me everything about program X" and you're calling 4 tools:

```yaml
# Add to impact-analysis.yaml
ia_program_full_analysis:
  name: ia-program-full-analysis
  source: ibmi
  description: >
    Complete program analysis in one query: calls, files, variables, 
    complexity metrics, and lifecycle data. Use instead of calling
    multiple tools separately.
  parameters:
    - name: program_name
      type: string
      required: true
      maxLength: 10
  statement: |
    SELECT ... (combined SQL joining multiple tables)
```

After adding, update this skill to reference the new tool.
