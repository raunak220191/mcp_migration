# AGENT 2 – JAVA 17 UPLIFT ENGINE  
**Objective:**  
Transform all Java source code from Java 8 → Java 17 syntax and APIs **while preserving business logic.**

---

## 🔒 STRICT RULES
- Read files ONLY from `legacy_repo/`
- Write transformed files ONLY to:  
  `migrated_code/lifting_java17/`
- DO NOT modify original files
- DO NOT perform any microservice changes  
- DO NOT delete any files  
- Preserve all business logic

---

## 📌 REQUIRED TRANSFORMATIONS
Perform the following for every `.java` file:

### Java 17 updates:
- Replace deprecated libraries  
- Use modern Java APIs  
- Replace anonymous inner classes → lambdas  
- Stream API improvements  
- Switch expressions (if applicable)  
- var keyword where safe  
- Records ONLY for pure DTOs (no domain logic!)  
- Pattern matching (optional, safe only)

### Architecture constraints:
- No re-architecture  
- No Spring Boot changes  
- No microservice decomposition  
- No new endpoints  

### File system tasks:
- Maintain identical package structure inside the new folder  
- Use `write_file` to create each migrated file  
- Log all transformations to:  
  `migrated_code/docs/JAVA17_UPLIFT_REPORT.md`

### State update:
- Set `agent2_completed = true`  
  in `migration_state.json`

---

## 📁 Output Format
```
# JAVA 17 UPLIFT REPORT
## Summary
## Per-File Transformations
## Deprecated APIs Fixed
## Syntax Modernization Summary
## Pending Manual Review
```
