# Power User Guide — What Can You Ask?

This guide shows you everything you can ask your AI assistant when connected to your IBM i Impact Analysis repository. Just type these questions in plain English — the assistant figures out the rest.

No SQL. No commands. Just ask.

---

## Finding Who Uses What

> _"What programs, service programs, files, or commands depend on this object?"_

- **Find all programs that reference CUSTMAST**
- **What objects use ORDERFILE?**
- **Show me everything that depends on CUSTPGM**
- **Which service programs reference PRICECALC?**
- **Which display files use CUSTFILE?**
- **Which commands reference INVPGM?**
- **What logical files are built on top of CUSTMAST?**
- **List everything that references ORDERHDR — programs, files, service programs, all of it**
- **How many objects reference CUSTMAST? Just give me the count by type**
- **Is CUSTMAST heavily referenced or lightly referenced? Show me the numbers**
- **Which objects reference IASRV01SV in the #IAOBJ library, and do they still have source code?**
- **Find all callers of ORDPGM and tell me which ones have editable source vs. compiled-only**

---

## Call Hierarchy — Who Calls Whom

> _"Trace the call chain — upstream, downstream, or both"_

- **What programs does ORDERPGM call?**
- **Who calls CUSTUPD?**
- **Show the full call tree for INVMAINT — both callers and callees**
- **What is the complete call chain starting from MAINMENU?**
- **Which programs call PRICECALC, and what procedures do they use?**
- **Trace every program that eventually reaches DBUPDATE**
- **Are there any circular dependencies in the codebase? Programs that call each other?**
- **Show me programs that call each other in a loop — these are maintenance risks**
- **What does the batch scheduler program NIGHTRUN call?**
- **Starting from ORDENTRY, map out the entire downstream call tree**

---

## Field-Level Impact Analysis

> _"What breaks if I change this field?"_

- **What is the blast radius of changing field CUSTNO in ORDERFILE?**
- **If I resize ORDAMT in ORDERHDR, which programs are affected?**
- **Which programs use the CUSTNO field from CUSTMAST?**
- **Show me every program that touches field PRICE in ITEMFILE**
- **I need to rename field ADDR1 in CUSTMAST — what will break?**
- **What is the impact of changing the data type of ORDQTY in ORDERDET?**
- **List all the fields in CUSTMAST with their data types and record formats**
- **What fields does ORDERFILE have? Show me the full schema**
- **Which fields in INVFILE are referenced by other files?**
- **Show me field details for CUSTMAST — types, lengths, reference files, everything**

---

## Program Internals — Deep Dive

> _"Understand what's inside a program without reading the source line by line"_

### Variables

- **List all variables declared in ORDHIST**
- **What variables does CUSTUPD use? Show me types and lengths**
- **Are there any arrays declared in INVMAINT?**
- **Show me all standalone fields in ORDERPGM**
- **What indicators does CUSTPGM use?**
- **How is variable WKCUSTNO used in ORDERPGM? Show me every assignment, comparison, and declaration**
- **Find all EVAL operations involving CUSTNO in CUSTUPD**
- **What built-in functions are used with variable ORDDATE in INVMAINT?**
- **Trace the data flow for WKPRICE through ORDERPGM — where is it set and where is it read?**

### Data Structures

- **What data structures are defined in CUSTUPD?**
- **Show me the subfields of data structure CUSTDS in ORDERPGM**
- **Are there any qualified data structures in INVMAINT?**
- **Which data structures in ORDHIST are based on a pointer?**
- **Show me the positional layout of DS ORDER_DS in ORDERPGM**

### Subroutines

- **What subroutines does ORDERPGM have?**
- **Are there any dead subroutines in CUSTUPD — defined but never called?**
- **How many times is subroutine VALIDATE called in INVMAINT?**
- **List all BEGSR/ENDSR blocks in ORDHIST with their usage counts**
- **Which subroutines in CUSTPGM are only used once?**

### Procedures

- **What is the parameter signature of procedure GETCUSTOMER?**
- **Show me the PR and PI declarations for procedure CALCPRICE**
- **What parameters does VALIDATEORDER expect — types, lengths, CONST/VALUE keywords?**
- **Which programs call procedure UPDATEINV?**
- **What procedures does GETCUSTOMER call?**
- **Show me all procedure-to-procedure relationships for CALCPRICE**
- **Find every caller of procedure WRITELOG across all programs**

### Key Lists

- **What KLIST definitions does ORDERPGM use?**
- **Show me the key fields in key list CUSTKEY in CUSTUPD**
- **Which programs use key list ORDKEY?**
- **List all KLIST/KFLD definitions across the entire codebase**

---

## Program Overview & Structure

> _"Get the big picture on a program fast"_

- **Give me a quick summary of ORDERPGM — source info, complexity, line counts**
- **What are the compile details for CUSTUPD?**
- **What source file and library does INVMAINT come from?**
- **When was ORDERPGM last compiled?**
- **Show me a deep structural breakdown of CUSTUPD — calls, files, subroutines, variables, overrides, everything**
- **What files does ORDERPGM use?**
- **What calls does INVMAINT make and what parameters does it pass?**
- **Show me just the file references for CUSTUPD**
- **Show me just the subroutines and variables for ORDERPGM**
- **What is the target release for CUSTPGM?**

---

## File Usage & Overrides

> _"Understand the real file routing, not just what's declared"_

- **What files does ORDERPGM access, including prefix and rename details?**
- **Show me file overrides (OVRDBF) in CUSTUPD — which files get redirected at runtime?**
- **Are there any chained file overrides? Where file A is overridden to B, and B is overridden to C?**
- **Show me all file overrides across the entire codebase**
- **Which programs override CUSTMAST to a different file?**
- **What record formats does ORDERPGM use for ORDERFILE?**
- **Does INVMAINT rename any files or record formats?**
- **Show me the PREFIX mappings in CUSTUPD**

---

## File Dependencies — Logical Files, Indexes, Views

> _"What's built on top of this physical file?"_

- **What logical files depend on CUSTMAST?**
- **Show me all indexes and views over ORDERFILE**
- **What SQL views are built on INVFILE?**
- **Are there any constraints defined on CUSTMAST?**
- **List all access path dependencies for ORDERHDR**
- **If I change CUSTMAST, what logical files and indexes are also affected?**

---

## Source Code — Reading & Searching

> _"Read and search actual RPG source code"_

### Reading Source

- **Show me the source code for ORDERPGM**
- **Show me just the D-specs (definitions) from CUSTUPD**
- **Show me only the calculation specs for INVMAINT**
- **What are the file declarations (F-specs) in ORDERPGM?**
- **Show me the procedure boundaries (P-specs) in CUSTUPD**
- **Show me the header specs for INVMAINT**
- **Where is the source for CUSTUPD stored? What file, library, and member type?**
- **How many lines of code does ORDERPGM have? How many are comments?**

### Searching Across Source

- **Search all RPG source for references to QCMDEXC**
- **Find all programs that contain embedded SQL with the table name CUSTMAST**
- **Which RPG members contain the text "GOTO"?**
- **Search for hardcoded value '99999' across all source members**
- **Find all uses of %TIMESTAMP across the RPG codebase**
- **Which programs reference the API QUSRJOBI?**
- **Search for deprecated MOVE operations across all RPGLE source**
- **Find all source members that contain "SQLSTATE"**
- **Search for "COMMIT" across all SQLRPGLE members only**

### Token-Level Analysis

- **Show me a token-level breakdown of ORDERPGM's RPG source**
- **Show me the parsed tokens for CL member NIGHTRUN**

---

## Dead Code & Cleanup Candidates

> _"What can we safely retire?"_

- **Which programs are never referenced by anything? Show me dead code candidates**
- **Find all unused service programs**
- **Are there any files that nothing references?**
- **Which source members have never been compiled? Show me orphaned sources**
- **Find RPGLE source members with no compiled object**
- **Find SQLRPGLE members that were never compiled**
- **Show me all uncompiled CL sources**
- **Which programs are both unreferenced AND haven't been used in over a year?** _(combines unused detection with lifecycle data)_
- **Are there subroutines in ORDERPGM that are defined but never called?** _(dead subroutines within a program)_

---

## Code Complexity & Modernization

> _"Where are the riskiest programs? How modern is our code?"_

### Complexity Metrics

- **Which programs are the most complex in the entire codebase?**
- **Show me complexity metrics for ORDERPGM — IF counts, DO loops, SQL statements, GOTO usage**
- **Rank all programs by total lines of code, highest first**
- **Which programs use GOTO? These are refactoring candidates**
- **Which programs have the most subroutines?**
- **Show me programs with the highest SQL statement count**
- **Which programs call the most other programs?**
- **Which programs are called by the most other programs?**
- **Find programs that have display files — these are interactive programs**
- **List all programs sorted by executable line count**

### Modernization Readiness

- **What percentage of ORDERPGM is free-format vs. fixed-format RPG?**
- **Show me the modernization readiness of every RPG member — free-format percentage for each**
- **Which programs are still 100% fixed-format? These need modernization first**
- **Which programs are already mostly free-format?**
- **Compare the free-format percentage of ORDERPGM across different libraries**
- **What is the comment-to-code ratio for CUSTUPD?**
- **Show me the overall portfolio modernization dashboard — every member with format stats**
- **Which SQLRPGLE members have the lowest free-format percentage?**

---

## Object Lifecycle & Freshness

> _"When was this created, last changed, or last used?"_

- **When was ORDERPGM created?**
- **When was CUSTMAST last modified?**
- **When was INVPGM last used?**
- **How many days has OLDPGM been in use?**
- **Show me the lifecycle for CUSTUPD — creation, last change, last used dates**
- **Is BATCHPGM stale? When was it last accessed?**
- **Find objects that haven't been used recently** _(combine lifecycle with unused detection)_

---

## Copybook Impact

> _"What breaks if I change this copybook?"_

- **Which programs include copybook CUSTDS via /COPY?**
- **What is the blast radius of changing copybook ABORTERR?**
- **Show me all members that use /COPY or /INCLUDE for ERRHANDLR**
- **How many RPGLE programs include CUSTDS?**
- **How many SQLRPGLE programs include CUSTDS?**
- **Find every source member that includes copybook COMMONPR — I need to know the full impact before I edit it**

---

## Batch Jobs & Scheduling

> _"What runs in batch? What gets submitted to job queues?"_

- **Which CL programs submit jobs (SBMJOB)?**
- **What job queues are used in the codebase?**
- **Show me all SBMJOB calls in NIGHTRUN — what programs does it submit?**
- **Which CL programs have direct CALL commands vs. submitted jobs?**
- **Are there any jobs submitted with HOLD status?**
- **What job descriptions are used across all CL programs?**
- **Find programs that look unused but are actually invoked via SBMJOB** _(explains why some "unused" programs matter)_

---

## Service Programs & APIs

> _"What does this service program expose? What does it depend on?"_

- **What procedures does service program CUTSRVPGM export?**
- **What external procedures does ORDSRV import?**
- **Show me both the exports and imports for PRICESRV**
- **Which programs call the exported procedure GETCUSTOMER from CUSTSRV?**
- **What is the complete API surface of service program UTILSRV?**
- **Which service programs depend on external procedures from other service programs?**

---

## Application Areas & Scoping

> _"Organize analysis by business area or project"_

- **What application areas are defined in the repository?**
- **Which objects belong to the "Order Processing" application area?**
- **Show me all programs in the "Customer Management" area**
- **What files are part of the "Inventory" application area?**
- **List application areas and who created them**

---

## Object Discovery & Lookup

> _"Find objects when you only know part of the name"_

- **Look up CUSTMAST — what type is it and what library is it in?**
- **Search for any object with "CUST" in the name**
- **Find all objects matching %ORDER%**
- **What type of object is NIGHTRUN?**
- **Is INVPGM a program or a service program?**
- **List all programs in the repository**
- **List all service programs in the repository**
- **List all files in the repository**
- **Show me all display files**
- **List all commands in the repository**
- **What data areas exist?**
- **Show me all modules in the repository**

---

## SQL Name Resolution

> _"Map between long SQL names and short system names"_

- **What is the system short name for SQL procedure CALCULATE_ORDER_TOTAL?**
- **What SQL long name maps to system name STORE00001?**
- **Search for any SQL routine with "CUSTOMER" in the name**
- **List all SQL procedures and their system short names**
- **List all SQL functions with their mappings**
- **Find the source member for SQL procedure GET_PRICE**

---

## Repository Health & Configuration

> _"What's the overall state of the repository?"_

- **Show me the repository health dashboard — how many compiled, unused, and orphaned members are there?**
- **What does the dashboard look like broken down by member type?**
- **What are the current repository configuration settings?**
- **What's the IFS scan path configured in the repo?**
- **Are there any parser exceptions? Which members had problems during parsing?**
- **Show me parser errors for CUSTUPD — did it parse correctly?**
- **What is the DDS to DDL conversion status? Which files have been converted to SQL?**
- **Which files are still pending DDS-to-DDL conversion?**
- **How many files and tables exist in the repository library?**
- **List all tables in the repository**
- **List all views in the repository**

---

## Cross-Cutting Queries — Combining Multiple Angles

> _"These questions pull together multiple perspectives for deeper insight"_

### Change Impact Assessment

- **I'm planning to change CUSTMAST. Give me the full impact: which programs use it, what logical files depend on it, what fields it has, and which copybooks are involved**
- **Before I modify field CUSTNO in CUSTMAST: show me every program that references this file, every logical file built on it, and the field's data type**
- **What is the total blast radius of renaming ORDERFILE? Show me programs, logical files, indexes, and any file overrides that redirect to it**

### Program Understanding

- **I need to fully understand ORDERPGM. Show me: source info, what it calls, who calls it, its variables, data structures, subroutines, file usage, and complexity metrics**
- **Give me a complete picture of CUSTUPD: its call tree, the files it uses, any file overrides, parameters it passes to called programs, and its source code**
- **Profile INVMAINT: how complex is it, how modern is the code (free-format %), what does it call, and when was it last compiled?**

### Modernization Planning

- **Which programs are the best candidates for modernization? Show me programs that are 100% fixed-format, highly complex, and heavily referenced**
- **Build me a modernization priority list: programs sorted by complexity that are still fixed-format and have many callers**
- **Which programs use GOTO and are also highly complex? These are the riskiest to maintain**
- **What percentage of our entire RPG portfolio is free-format vs. fixed-format?**

### Dead Code Audit

- **Run a full dead code audit: find programs nobody calls, source members never compiled, and objects not used in over a year**
- **Which "unused" programs are actually invoked by batch jobs (SBMJOB) and shouldn't be deleted?**
- **Cross-reference unused objects with lifecycle data — which ones were never used even once?**
- **Find service programs that export procedures nobody calls**

### Dependency Mapping

- **Map the complete dependency graph starting from MAINMENU — every program it reaches, directly or indirectly**
- **Which programs are the most interconnected? Show me objects with the highest reference counts**
- **Are there any circular dependencies that could cause infinite loops?**
- **What are the most referenced files in the entire system? These are the highest-risk files to change**
- **Which programs are leaf nodes — they call nothing else?**
- **Which programs are entry points — nothing calls them but they call other things?**

### Data Flow Tracing

- **Trace how customer number flows through the system: start from field CUSTNO in CUSTMAST, find every program that uses it, and what variables they store it in**
- **Which programs both read CUSTMAST and write to ORDERFILE? These are the integration points**
- **Show me all programs that override CUSTMAST to a different file — the declared file access doesn't match reality**

### Batch & Scheduling Analysis

- **Map out the nightly batch flow: what does NIGHTRUN submit, what does each submitted job call, and what files do they all touch?**
- **Which batch jobs are submitted on hold? Someone has to release them manually**
- **Find CL programs that submit jobs to QBATCH job queue**

---

## What to Expect in Responses

When you ask these questions, you'll typically receive:

| You Ask About | You'll Get Back |
|---------------|-----------------|
| Who uses an object | A table of referencing programs, their types, and how they reference it |
| Call hierarchy | A tree showing caller/callee relationships with procedure-level detail |
| Field impact | A list of every affected program with the field's data type and usage mode |
| Variables / data structures | A detailed listing with types, lengths, decimal positions, and array dimensions |
| Subroutines | Names, opcodes, usage counts, and line numbers |
| Complexity metrics | Counts for IF, DO, SQL, GOTO, procedures, total lines, executable lines |
| Unused objects | A list of objects that no other object references — cleanup candidates |
| Source code | The actual source lines with line numbers and spec-type classification |
| Source search | Matching lines from across all members with member name and line number |
| Lifecycle dates | Creation, last-change, and last-used timestamps with days-used counts |
| File dependencies | Logical files, indexes, views, and constraints built on a physical file |
| Copybook impact | Every member that includes the copybook, with the exact line of the /COPY directive |
| Batch jobs | SBMJOB details including job queue, job description, and hold status |
| Service program APIs | Exported and imported procedure names with object details |
| Modernization stats | Free-format vs. fixed-format line counts and percentages per member |
| File overrides | From-file, to-file, scope, and any chained overrides |
| Procedure signatures | Parameter names, types, lengths, keywords (CONST, VALUE), and sequence |
| Object lookup | Object name, type, library, and attribute — with wildcard support |
| Dashboard | Member counts by category (compiled, unused, orphaned) with line totals |

---

## Tips for Getting the Best Results

1. **Be specific with names** — Use exact IBM i object names (up to 10 characters) when you know them: `CUSTMAST`, `ORDERPGM`, `NIGHTRUN`

2. **Use wildcards when exploring** — If you don't know the exact name, ask with partial names: _"Find anything with CUST in the name"_

3. **Start broad, then narrow** — Ask _"What references CUSTMAST?"_ first, then drill into specific programs: _"Show me the variables in CUSTUPD"_

4. **Combine perspectives** — The most powerful queries combine multiple angles: _"Show me who calls ORDERPGM, what it calls, its complexity, and when it was last compiled"_

5. **Ask follow-ups** — After seeing results, dig deeper: _"Now show me the source code for that program"_ or _"What are the parameters it passes?"_

6. **Think about blast radius** — Before any change, ask: _"What is the impact of changing X?"_ — this is the single most valuable question you can ask

---

_This guide covers the full range of questions you can ask. The AI assistant selects the right approach automatically — just ask in plain English._
