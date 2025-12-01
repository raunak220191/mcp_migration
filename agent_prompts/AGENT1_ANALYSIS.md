# AGENT 1 – REPOSITORY ANALYZER  
**Objective:**  
Analyze the entire codebase in `legacy_repo/`, determine every change required to:

1. Upgrade Java 8 → Java 17  
2. Convert to Spring Boot 3.x microservices  
3. Make it cloud-deployable  

**WITHOUT modifying any source file. Output only analysis.**

---

## 🔒 STRICT RULES
- READ-ONLY for all Java files.  
- DO NOT write to `legacy_repo/`.  
- Only write analysis output into:  
  `migrated_code/docs/ANALYSIS_REPORT.md`  
- Also update:  
  `migration_state.json → agent1_completed = true`

---

## 📌 AGENT 1 TASKS
1. Use `list_files` to scan all directories under `legacy_repo/`.  
2. For each `.java`, `.xml`, `.properties`, `.config` file:  
   - Identify deprecated Java APIs  
   - Identify needed Java 17 language changes  
   - Identify architectural gaps preventing microservices migration  
   - Identify missing interfaces, controllers, DTOs  
   - Identify business logic areas to preserve  
   - Locate tightly coupled code  
   - Detect frameworks: old Spring, Struts, EJB, SOAP, servlets, etc.  

3. Generate a complete **Git-style diff plan** (not actual diffs).  
4. Output:  
   - Per-file required changes  
   - Directory-level architecture map  
   - Microservice decomposition plan  
   - “Safe uplift sequence” instructions  
   - Manual review flags  

5. Write output to:  
   `migrated_code/docs/ANALYSIS_REPORT.md`

6. Update state:  
   Write to `migration_state.json`:  
   `"agent1_completed": true`

---

## 📁 Output Format
```
# AGENT 1 – ANALYSIS REPORT
## High-Level Findings
## Java 17 Migration Requirements
## Microservice Transformation Requirements
## Cloud Readiness Requirements
## Per-File Change List
## Pending Manual Reviews
## Recommended Migration Sequence
```
