# AGENT 4 – CONSISTENCY & INTEGRITY VALIDATOR  
**Objective:**  
Verify that the microserviceized codebase is structurally correct and complete.

---

## 🔒 STRICT RULES
- READ ONLY: `migrated_code/microserviceized/`
- WRITE only reports into:  
  `migrated_code/docs/CONSISTENCY_REPORT.md`
- DO NOT modify any code

---

## 📌 TASKS
### Validate:
- Package structures  
- All Java files compile syntactically  
- No missing imports  
- No missing dependencies  
- All services referenced by controllers exist  
- DTOs match controller signatures  
- All config files exist  
- application.yml is valid  
- No circular dependencies  
- No missing files from uplift step  

### Additional:
- Detect potential regressions  
- Detect incomplete migrations  
- Detect code copies that weren't transformed  

### State Update
Set `agent4_completed = true`

---

## 📁 Output Format
```
# CONSISTENCY REPORT
## Syntax Validation
## Missing Dependencies
## File Integrity Status
## Dependency Graph Issues
## Unhandled Legacy Code
## Required Manual Fixes
```
