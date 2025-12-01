# MASTER AGENT – ORCHESTRATOR  
**Objective:**  
Run and supervise Agents 1–5, ensure each operates strictly within scope, record their completion status, generate documentation, and ensure resumability using `migration_state.json`.

---

## 🔒 STRICT RULES
- Do NOT modify `legacy_repo/` files.
- Do NOT create new logic—preserve original business logic across all stages.
- All generated code must go inside `migrated_code/`.
- All documentation must go inside `migrated_code/docs/`.
- Update `migration_state.json` after each agent completes.
- If any agent fails, resume from the last safe step recorded in `migration_state.json`.

---

## 📌 TASKS FOR MASTER AGENT
1. Load the current state from `migration_state.json`.  
2. For each agent (1 → 5):  
   - If not completed → instruct human to run that agent’s `.md` file.  
   - After completion → update the state file.  
3. After all 5 agents finish, generate **6 final documents**:
   - `EXECUTIVE_SUMMARY.md`
   - `FULL_CHANGELOG.md`
   - `MANUAL_REVIEW_STEPS.md`
   - `TRANSFORMATION_REPORT_AGENT1.md`
   - `TRANSFORMATION_REPORT_AGENT2.md`
   - `TRANSFORMATION_REPORT_AGENT3_4_5.md`

4. Ensure documentation includes:
   - What was changed  
   - Why it was changed  
   - Per-file migration notes  
   - Pending manual fixes  
   - Structural transformations  
   - Microservice architecture map  
   - Cloud readiness summary  

---

## 📁 Output Locations
- `migrated_code/docs/*.md`
- `migration_state.json`

---

## ▶️ MASTER EXECUTION LOGIC  
Use MCP tools to:

1. `read_file("./migration_state.json")`
2. Check boolean flags:
   - `"agent1_completed"`
   - `"agent2_completed"`
   - `"agent3_completed"`
   - `"agent4_completed"`
   - `"agent5_completed"`

3. Produce:
   - A summary of completed tasks
   - A list of next agents to run
   - A final “All completed” message once all 5 agents have run

4. After finalization:
   - Use `write_file` to generate all documentation files.

---

## 💬 MASTER OUTPUT FORMAT
```
STATE SUMMARY
NEXT ACTIONS
REQUIRED AGENT EXECUTIONS
POST-COMPLETION DOCUMENTATION
MASTER COMPLETION SIGNAL
```
